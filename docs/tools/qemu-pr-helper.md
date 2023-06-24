# QEMU persistent reservation helper

## Synopsis

**qemu-pr-helper** \[*OPTION*\]

## Description

Implements the persistent reservation helper for QEMU.

SCSI persistent reservations allow restricting access to block devices
to specific initiators in a shared storage setup. When implementing
clustering of virtual machines, it is a common requirement for virtual
machines to send persistent reservation SCSI commands. However, the
operating system restricts sending these commands to unprivileged
programs because incorrect usage can disrupt regular operation of the
storage fabric. QEMU\'s SCSI passthrough devices `scsi-block` and
`scsi-generic` support passing guest persistent reservation requests to
a privileged external helper program. `qemu-pr-helper`{.interpreted-text
role="program"} is that external helper; it creates a listener socket
which will accept incoming connections for communication with QEMU.

If you want to run VMs in a setup like this, this helper should be
started as a system service, and you should read the QEMU manual section
on \"persistent reservation managers\" to find out how to configure QEMU
to connect to the socket created by `qemu-pr-helper`{.interpreted-text
role="program"}.

After connecting to the socket, `qemu-pr-helper`{.interpreted-text
role="program"} can optionally drop root privileges, except for those
capabilities that are needed for its operation.

`qemu-pr-helper`{.interpreted-text role="program"} can also use the
systemd socket activation protocol. In this case, the systemd socket
unit should specify a Unix stream socket, like this:

    [Socket]
    ListenStream=/var/run/qemu-pr-helper.sock

## Options

::: program
qemu-pr-helper
:::

::: option
-d, \--daemon

run in the background (and create a PID file)
:::

::: option
-q, \--quiet

decrease verbosity
:::

::: option
-v, \--verbose

increase verbosity
:::

::: option
-f, \--pidfile=PATH

PID file when running as a daemon. By default the PID file is created in
the system runtime state directory, for example
`/var/run/qemu-pr-helper.pid`{.interpreted-text role="file"}.
:::

::: option
-k, \--socket=PATH

path to the socket. By default the socket is created in the system
runtime state directory, for example
`/var/run/qemu-pr-helper.sock`{.interpreted-text role="file"}.
:::

::: option
-T, \--trace \[\[enable=\]PATTERN\]\[,events=FILE\]\[,file=FILE\]

Specify tracing options.

`[enable=]PATTERN`

> Immediately enable events matching *PATTERN* (either event name or a
> globbing pattern). This option is only available if QEMU has been
> compiled with the `simple`, `log` or `ftrace` tracing backend. To
> specify multiple events or patterns, specify the
> `-trace`{.interpreted-text role="option"} option multiple times.
>
> Use `-trace help`{.interpreted-text role="option"} to print a list of
> names of trace points.

`events=FILE`

> Immediately enable events listed in *FILE*. The file must contain one
> event name (as listed in the `trace-events-all` file) per line;
> globbing patterns are accepted too. This option is only available if
> QEMU has been compiled with the `simple`, `log` or `ftrace` tracing
> backend.

`file=FILE`

> Log output traces to *FILE*. This option is only available if QEMU has
> been compiled with the `simple` tracing backend.
:::

::: option
-u, \--user=USER

user to drop privileges to
:::

::: option
-g, \--group=GROUP

group to drop privileges to
:::

::: option
-h, \--help

Display a help message and exit.
:::

::: option
-V, \--version

Display version information and exit.
:::
