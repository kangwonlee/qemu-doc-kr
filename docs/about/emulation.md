# Emulation

QEMU\'s Tiny Code Generator (TCG) provides the ability to emulate a
number of CPU architectures on any supported host platform. Both
`System Emulation`{.interpreted-text role="ref"} and
`User Mode Emulation`{.interpreted-text role="ref"} are supported
depending on the guest architecture.

  ---------------------------------------------------------------------------------------------------------------------
  Architecture (qemu    System                                              User    Notes
  name)                                                                             
  --------------------- --------------------------------------------------- ------- -----------------------------------
  Alpha                 Yes                                                 Yes     Legacy 64 bit RISC ISA developed by
                                                                                    DEC

  Arm (arm, aarch64)    `Yes<ARM-System-emulator>`{.interpreted-text        Yes     Wide range of features, see
                        role="ref"}                                                 `Arm Emulation`{.interpreted-text
                                                                                    role="ref"} for details

  AVR                   `Yes<AVR-System-emulator>`{.interpreted-text        No      8 bit micro controller, often used
                        role="ref"}                                                 in maker projects

  Cris                  Yes                                                 Yes     Embedded RISC chip developed by
                                                                                    AXIS

  Hexagon               No                                                  Yes     Family of DSPs by Qualcomm

  PA-RISC (hppa)        Yes                                                 Yes     A legacy RISC system used in HP\'s
                                                                                    old minicomputers

  x86 (i386, x86_64)    `Yes<QEMU-PC-System-emulator>`{.interpreted-text    Yes     The ubiquitous desktop PC CPU
                        role="ref"}                                                 architecture, 32 and 64 bit.

  Loongarch             Yes                                                 Yes     A MIPS-like 64bit RISC architecture
                                                                                    developed in China

  m68k                  `Yes<ColdFire-System-emulator>`{.interpreted-text   Yes     Motorola 68000 variants and
                        role="ref"}                                                 ColdFire

  Microblaze            Yes                                                 Yes     RISC based soft-core by Xilinx

  MIPS (mips\*)         `Yes<MIPS-System-emulator>`{.interpreted-text       Yes     Venerable RISC architecture
                        role="ref"}                                                 originally out of Stanford
                                                                                    University

  Nios2                 Yes                                                 Yes     32 bit embedded soft-core by Altera

  OpenRISC              `Yes<OpenRISC-System-emulator>`{.interpreted-text   Yes     Open source RISC architecture
                        role="ref"}                                                 developed by the OpenRISC community

  Power (ppc, ppc64)    `Yes<PowerPC-System-emulator>`{.interpreted-text    Yes     A general purpose RISC architecture
                        role="ref"}                                                 now managed by IBM

  RISC-V                `Yes<RISC-V-System-emulator>`{.interpreted-text     Yes     An open standard RISC ISA
                        role="ref"}                                                 maintained by RISC-V International

  RX                    `Yes<RX-System-emulator>`{.interpreted-text         No      A 32 bit micro controller developed
                        role="ref"}                                                 by Renesas

  s390x                 `Yes<s390x-System-emulator>`{.interpreted-text      Yes     A 64 bit CPU found in IBM\'s System
                        role="ref"}                                                 Z mainframes

  sh4                   Yes                                                 Yes     A 32 bit RISC embedded CPU
                                                                                    developed by Hitachi

  SPARC (sparc,         `Yes<Sparc32-System-emulator>`{.interpreted-text    Yes     A RISC ISA originally developed by
  sparc64)              role="ref"}                                                 Sun Microsystems

  Tricore               Yes                                                 No      A 32 bit RISC/uController/DSP
                                                                                    developed by Infineon

  Xtensa                `Yes<Xtensa-System-emulator>`{.interpreted-text     Yes     A configurable 32 bit soft core now
                        role="ref"}                                                 owned by Cadence
  ---------------------------------------------------------------------------------------------------------------------

  : Supported Guest Architectures for Emulation

A number of features are only available when running under emulation
including `Record/Replay<replay>`{.interpreted-text role="ref"} and
`TCG Plugins`{.interpreted-text role="ref"}.

## Semihosting {#Semihosting}

Semihosting is a feature defined by the owner of the architecture to
allow programs to interact with a debugging host system. On real
hardware this is usually provided by an In-circuit emulator (ICE) hooked
directly to the board. QEMU\'s implementation allows for semihosting
calls to be passed to the host system or via the `gdbstub`.

Generally semihosting makes it easier to bring up low level code before
a more fully functional operating system has been enabled. On QEMU it
also allows for embedded micro-controller code which typically doesn\'t
have a full libc to be run as \"bare-metal\" code under QEMU\'s
user-mode emulation. It is also useful for writing test cases and indeed
a number of compiler suites as well as QEMU itself use semihosting calls
to exit test code while reporting the success state.

Semihosting is only available using TCG emulation. This is because the
instructions to trigger a semihosting call are typically reserved
causing most hypervisors to trap and fault on them.

::: warning
::: title
Warning
:::

Semihosting inherently bypasses any isolation there may be between the
guest and the host. As a result a program using semihosting can happily
trash your host system. You should only ever run trusted code with
semihosting enabled.
:::

### Redirection

Semihosting calls can be re-directed to a (potentially remote) gdb
during debugging via the `gdbstub<GDB usage>`{.interpreted-text
role="ref"}. Output to the semihosting console is configured as a
`chardev` so can be redirected to a file, pipe or socket like any other
`chardev` device.

### Supported Targets

Most targets offer similar semihosting implementations with some minor
changes to define the appropriate instruction to encode the semihosting
call and which registers hold the parameters. They tend to presents a
simple POSIX-like API which allows your program to read and write files,
access the console and some other basic interactions.

For full details of the ABI for a particular target, and the set of
calls it provides, you should consult the semihosting specification for
that architecture.

::: note
::: title
Note
:::

QEMU makes an implementation decision to implement all file access in
`O_BINARY` mode. The user-visible effect of this is regardless of the
text/binary mode the program sets QEMU will always select a binary mode
ensuring no line-terminator conversion is performed on input or output.
This is because gdb semihosting support doesn\'t make the distinction
between the modes and magically processing line endings can be
confusing.
:::

  ---------------------------------------------------------------------------------------------------------------------------------------
  Architecture   Modes       Specification
  -------------- ----------- ------------------------------------------------------------------------------------------------------------
  Arm            System and  <https://github.com/ARM-software/abi-aa/blob/main/semihosting/semihosting.rst>
                 User-mode   

  m68k           System      <https://sourceware.org/git/?p=newlib-cygwin.git;a=blob;f=libgloss/m68k/m68k-semi.txt;hb=HEAD>

  MIPS           System      Unified Hosting Interface (MD01069)

  Nios II        System      <https://sourceware.org/git/gitweb.cgi?p=newlib-cygwin.git;a=blob;f=libgloss/nios2/nios2-semi.txt;hb=HEAD>

  RISC-V         System and  <https://github.com/riscv/riscv-semihosting-spec/blob/main/riscv-semihosting-spec.adoc>
                 User-mode   

  Xtensa         System      Tensilica ISS SIMCALL
  ---------------------------------------------------------------------------------------------------------------------------------------

  : Guest Architectures supporting Semihosting
