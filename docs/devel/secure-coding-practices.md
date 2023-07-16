# Secure Coding Practices

This document covers topics that both developers and security
researchers must be aware of so that they can develop safe code and
audit existing code properly.

## Reporting Security Bugs

For details on how to report security bugs or ask questions about
potential security bugs, see the [Security Process wiki
page](https://wiki.qemu.org/SecurityProcess).

## General Secure C Coding Practices

Most CVEs (security bugs) reported against QEMU are not specific to
virtualization or emulation. They are simply C programming bugs.
Therefore it\'s critical to be aware of common classes of security bugs.

There is a wide selection of resources available covering secure C
coding. For example, the [CERT C Coding
Standard](https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard)
covers the most important classes of security bugs.

Instead of describing them in detail here, only the names of the most
important classes of security bugs are mentioned:

-   Buffer overflows
-   Use-after-free and double-free
-   Integer overflows
-   Format string vulnerabilities

Some of these classes of bugs can be detected by analyzers. Static
analysis is performed regularly by Coverity and the most obvious of
these bugs are even reported by compilers. Dynamic analysis is possible
with valgrind, tsan, and asan.

## Input Validation

Inputs from the guest or external sources (e.g. network, files) cannot
be trusted and may be invalid. Inputs must be checked before using them
in a way that could crash the program, expose host memory to the guest,
or otherwise be exploitable by an attacker.

The most sensitive attack surface is device emulation. All hardware
register accesses and data read from guest memory must be validated. A
typical example is a device that contains multiple units that are
selectable by the guest via an index register:

    typedef struct {
        ProcessingUnit unit[2];
        ...
    } MyDeviceState;

    static void mydev_writel(void *opaque, uint32_t addr, uint32_t val)
    {
        MyDeviceState *mydev = opaque;
        ProcessingUnit *unit;

        switch (addr) {
        case MYDEV_SELECT_UNIT:
            unit = &mydev->unit[val];   <-- this input wasn't validated!
            ...
        }
    }

If `val` is not in range \[0, 1\] then an out-of-bounds memory access
will take place when `unit` is dereferenced. The code must check that
`val` is 0 or 1 and handle the case where it is invalid.

## Unexpected Device Accesses

The guest may access device registers in unusual orders or at unexpected
moments. Device emulation code must not assume that the guest follows
the typical \"theory of operation\" presented in driver writer manuals.
The guest may make nonsense accesses to device registers such as
starting operations before the device has been fully initialized.

A related issue is that device emulation code must be prepared for
unexpected device register accesses while asynchronous operations are in
progress. A well-behaved guest might wait for a completion interrupt
before accessing certain device registers. Device emulation code must
handle the case where the guest overwrites registers or submits further
requests before an ongoing request completes. Unexpected accesses must
not cause memory corruption or leaks in QEMU.

Invalid device register accesses can be reported with
`qemu_log_mask(LOG_GUEST_ERROR, ...)`. The `-d guest_errors`
command-line option enables these log messages.

## Live Migration

Device state can be saved to disk image files and shared with other
users. Live migration code must validate inputs when loading device
state so an attacker cannot gain control by crafting invalid device
states. Device state is therefore considered untrusted even though it is
typically generated by QEMU itself.

## Guest Memory Access Races

Guests with multiple vCPUs may modify guest RAM while device emulation
code is running. Device emulation code must copy in descriptors and
other guest RAM structures and only process the local copy. This
prevents time-of-check-to-time-of-use (TOCTOU) race conditions that
could cause QEMU to crash when a vCPU thread modifies guest RAM while
device emulation is processing it.

## Use of null-co block drivers

The `null-co` block driver is designed for performance: its read
accesses are not initialized by default. In case this driver has to be
used for security research, it must be used with the `read-zeroes=on`
option which fills read buffers with zeroes. Security issues reported
with the default (`read-zeroes=off`) will be discarded.