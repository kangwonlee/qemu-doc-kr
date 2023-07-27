QEMU Monitor[¶](#qemu-monitor "Permalink to this headline")
===========================================================


The QEMU monitor is used to give complex commands to the QEMU emulator.
You can use it to:


* Remove or insert removable media images (such as CD-ROM or
floppies).
* Freeze/unfreeze the Virtual Machine (VM) and save or restore its
state from a disk file.
* Inspect the VM state without an external debugger.



Commands[¶](#commands "Permalink to this headline")
---------------------------------------------------


The following commands are available:



`help` or `?` [*cmd*]Show the help for all commands or just for command *cmd*.



`commit`Commit changes to the disk images (if -snapshot is used) or backing files.
If the backing file is smaller than the snapshot, then the backing file
will be resized to be the same size as the snapshot. If the snapshot is
smaller than the backing file, the backing file will not be truncated.
If you want the backing file to match the size of the smaller snapshot,
you can safely truncate it yourself once the commit operation successfully
completes.



`quit` or `q`Quit the emulator.



`exit\_preconfig`This command makes QEMU exit the preconfig state and proceed with
VM initialization using configuration data provided on the command line
and via the QMP monitor during the preconfig state. The command is only
available during the preconfig state (i.e. when the –preconfig command
line option was in use).



`block\_resize`Resize a block image while a guest is running. Usually requires guest
action to see the updated size. Resize to a lower size is supported,
but should be used with extreme caution. Note that this command only
resizes image files, it can not resize block devices like LVM volumes.



`block\_stream`Copy data from a backing file into a block device.



`block\_job\_set\_speed`Set maximum speed for a background block operation.



`block\_job\_cancel`Stop an active background block operation (streaming, mirroring).



`block\_job\_complete`Manually trigger completion of an active background block operation.
For mirroring, this will switch the device to the destination path.



`block\_job\_pause`Pause an active block streaming operation.



`block\_job\_resume`Resume a paused block streaming operation.



`eject [-f]` *device*Eject a removable medium (use -f to force it).



`drive\_del` *device*Remove host block device. The result is that guest generated IO is no longer
submitted against the host device underlying the disk. Once a drive has
been deleted, the QEMU Block layer returns -EIO which results in IO
errors in the guest for applications that are reading/writing to the device.
These errors are always reported to the guest, regardless of the drive’s error
actions (drive options rerror, werror).



`change` *device* *setting*Change the configuration of a device.



`change` *diskdevice* [-f] *filename* [*format* [*read-only-mode*]]Change the medium for a removable disk device to point to *filename*. eg:



```
(qemu) change ide1-cd0 /path/to/some.iso

```



`-f`forces the operation even if the guest has locked the tray.




*format* is optional.


*read-only-mode* may be used to change the read-only status of the device.
It accepts the following values:



retainRetains the current status; this is the default.



read-onlyMakes the device read-only.



read-writeMakes the device writable.






`change vnc password` [*password*]



> 
> Change the password associated with the VNC server. If the new password
> is not supplied, the monitor will prompt for it to be entered. VNC
> passwords are only significant up to 8 letters. eg:
> 
> 
> 
> ```
> (qemu) change vnc password
> Password: \*\*\*\*\*\*\*\*
> 
> ```
> 
> 
> 



`screendump` *filename*Save screen into PPM image *filename*.



`logfile` *filename*Output logs to *filename*.



`trace-event`changes status of a trace event



`trace-file on|off|flush`Open, close, or flush the trace file. If no argument is given, the
status of the trace file is displayed.



`log` *item1*[,…]Activate logging of the specified items.



`savevm` *tag*Create a snapshot of the whole virtual machine. If *tag* is
provided, it is used as human readable identifier. If there is already
a snapshot with the same tag, it is replaced. More info at
[VM snapshots](images.html#vm-005fsnapshots).


Since 4.0, savevm stopped allowing the snapshot id to be set, accepting
only *tag* as parameter.



`loadvm` *tag*Set the whole virtual machine to the snapshot identified by the tag
*tag*.


Since 4.0, loadvm stopped accepting snapshot id as parameter.



`delvm` *tag*Delete the snapshot identified by *tag*.


Since 4.0, delvm stopped deleting snapshots by snapshot id, accepting
only *tag* as parameter.



`one-insn-per-tb [off]`Run the emulation with one guest instruction per translation block.
This slows down emulation a lot, but can be useful in some situations,
such as when trying to analyse the logs produced by the `-d` option.
This only has an effect when using TCG, not with KVM or other accelerators.


If called with option off, the emulation returns to normal mode.



`singlestep [off]`This is a deprecated synonym for the one-insn-per-tb command.



`stop` or `s`Stop emulation.



`cont` or `c`Resume emulation.



`system\_wakeup`Wakeup guest from suspend.



`gdbserver` [*port*]Start gdbserver session (default *port*=1234)



`x/`*fmt* *addr*Virtual memory dump starting at *addr*.



`xp /`*fmt* *addr*Physical memory dump starting at *addr*.


*fmt* is a format which tells the command how to format the
data. Its syntax is: `/{count}{format}{size}`



*count*is the number of items to be dumped.



*format*can be x (hex), d (signed decimal), u (unsigned decimal), o (octal),
c (char) or i (asm instruction).



*size*can be b (8 bits), h (16 bits), w (32 bits) or g (64 bits). On x86,
`h` or `w` can be specified with the `i` format to
respectively select 16 or 32 bit code instruction size.




Examples:


Dump 10 instructions at the current instruction pointer:



```
(qemu) x/10i $eip
0x90107063:  ret
0x90107064:  sti
0x90107065:  lea    0x0(%esi,1),%esi
0x90107069:  lea    0x0(%edi,1),%edi
0x90107070:  ret
0x90107071:  jmp    0x90107080
0x90107073:  nop
0x90107074:  nop
0x90107075:  nop
0x90107076:  nop

```


Dump 80 16 bit values at the start of the video memory:



```
(qemu) xp/80hx 0xb8000
0x000b8000: 0x0b50 0x0b6c 0x0b65 0x0b78 0x0b38 0x0b36 0x0b2f 0x0b42
0x000b8010: 0x0b6f 0x0b63 0x0b68 0x0b73 0x0b20 0x0b56 0x0b47 0x0b41
0x000b8020: 0x0b42 0x0b69 0x0b6f 0x0b73 0x0b20 0x0b63 0x0b75 0x0b72
0x000b8030: 0x0b72 0x0b65 0x0b6e 0x0b74 0x0b2d 0x0b63 0x0b76 0x0b73
0x000b8040: 0x0b20 0x0b30 0x0b35 0x0b20 0x0b4e 0x0b6f 0x0b76 0x0b20
0x000b8050: 0x0b32 0x0b30 0x0b30 0x0b33 0x0720 0x0720 0x0720 0x0720
0x000b8060: 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720
0x000b8070: 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720
0x000b8080: 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720
0x000b8090: 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720 0x0720

```



`gpa2hva` *addr*Print the host virtual address at which the guest’s physical address *addr*
is mapped.



`gpa2hpa` *addr*Print the host physical address at which the guest’s physical address *addr*
is mapped.



`gva2gpa` *addr*Print the guest physical address at which the guest’s virtual address *addr*
is mapped based on the mapping for the current CPU.



`print` or `p/`*fmt* *expr*Print expression value. Only the *format* part of *fmt* is
used.



`i/`*fmt* *addr* [.*index*]Read I/O port.



`o/`*fmt* *addr* *val*Write to I/O port.



`sendkey` *keys*Send *keys* to the guest. *keys* could be the name of the
key or the raw value in hexadecimal format. Use `-` to press
several keys simultaneously. Example:



```
sendkey ctrl-alt-f1

```


This command is useful to send keys that your graphical user interface
intercepts at low level, such as `ctrl-alt-f1` in X Window.



`sync-profile [on|off|reset]`Enable, disable or reset synchronization profiling. With no arguments, prints
whether profiling is on or off.



`system\_reset`Reset the system.



`system\_powerdown`Power down the system (if supported).



`sum` *addr* *size*Compute the checksum of a memory region.



`device\_add` *config*Add device.



`device\_del` *id*Remove device *id*. *id* may be a short ID
or a QOM object path.



`cpu` *index*Set the default CPU.



`mouse\_move` *dx* *dy* [*dz*]Move the active mouse to the specified coordinates *dx* *dy*
with optional scroll axis *dz*.



`mouse\_button` *val*Change the active mouse button state *val* (1=L, 2=M, 4=R).



`mouse\_set` *index*Set which mouse device receives events at given *index*, index
can be obtained with:



```
info mice

```



`wavcapture` *filename* *audiodev* [*frequency* [*bits* [*channels*]]]Capture audio into *filename* from *audiodev*, using sample rate
*frequency* bits per sample *bits* and number of channels
*channels*.


Defaults:


* Sample rate = 44100 Hz - CD quality
* Bits = 16
* Number of channels = 2 - Stereo



`stopcapture` *index*Stop capture with a given *index*, index can be obtained with:



```
info capture

```



`memsave` *addr* *size* *file*save to disk virtual memory dump starting at *addr* of size *size*.



`pmemsave` *addr* *size* *file*save to disk physical memory dump starting at *addr* of size *size*.



`boot\_set` *bootdevicelist*Define new values for the boot device list. Those values will override
the values specified on the command line through the `-boot` option.


The values that can be specified here depend on the machine type, but are
the same that can be specified in the `-boot` command line option.



`nmi` *cpu*Inject an NMI on the default CPU (x86/s390) or all CPUs (ppc64).



`ringbuf\_write` *device* *data*Write *data* to ring buffer character device *device*.
*data* must be a UTF-8 string.



`ringbuf\_read` *device*Read and print up to *size* bytes from ring buffer character
device *device*.
Certain non-printable characters are printed `\uXXXX`, where `XXXX` is the
character code in hexadecimal. Character `\` is printed `\\`.
Bug: can screw up when the buffer contains invalid UTF-8 sequences,
NUL characters, after the ring buffer lost data, and when reading
stops because the size limit is reached.



`announce\_self`Trigger a round of GARP/RARP broadcasts; this is useful for explicitly
updating the network infrastructure after a reconfiguration or some forms
of migration. The timings of the round are set by the migration announce
parameters. An optional comma separated *interfaces* list restricts the
announce to the named set of interfaces. An optional *id* can be used to
start a separate announce timer and to change the parameters of it later.



`migrate [-d] [-b] [-i]` *uri*Migrate to *uri* (using -d to not wait for completion).



`-b`for migration with full copy of disk



`-i`for migration with incremental copy of disk (base image is shared)





`migrate\_cancel`Cancel the current VM migration.



`migrate\_continue` *state*Continue migration from the paused state *state*



`migrate\_incoming` *uri*Continue an incoming migration using the *uri* (that has the same syntax
as the `-incoming` option).



`migrate\_recover` *uri*Continue a paused incoming postcopy migration using the *uri*.



`migrate\_pause`Pause an ongoing migration. Currently it only supports postcopy.



`migrate\_set\_capability` *capability* *state*Enable/Disable the usage of a capability *capability* for migration.



`migrate\_set\_parameter` *parameter* *value*Set the parameter *parameter* for migration.



`migrate\_start\_postcopy`Switch in-progress migration to postcopy mode. Ignored after the end of
migration (or once already in postcopy).



`x\_colo\_lost\_heartbeat`Tell COLO that heartbeat is lost, a failover or takeover is needed.



`client\_migrate\_info` *protocol* *hostname* *port* *tls-port* *cert-subject*Set migration information for remote display. This makes the server
ask the client to automatically reconnect using the new parameters
once migration finished successfully. Only implemented for SPICE.



`dump-guest-memory [-p]` *filename* *begin* *length*

`dump-guest-memory [-z|-l|-s|-w]` *filename*Dump guest memory to *protocol*. The file can be processed with crash or
gdb. Without `-z|-l|-s|-w`, the dump format is ELF.



`-p`do paging to get guest’s memory mapping.



`-z`dump in kdump-compressed format, with zlib compression.



`-l`dump in kdump-compressed format, with lzo compression.



`-s`dump in kdump-compressed format, with snappy compression.



`-w`dump in Windows crashdump format (can be used instead of ELF-dump converting),
for Windows x64 guests with vmcoreinfo driver only



*filename*dump file name.



*begin*the starting physical address. It’s optional, and should be
specified together with *length*.



*length*the memory size, in bytes. It’s optional, and should be specified
together with *begin*.





`dump-skeys` *filename*Save guest storage keys to a file.



`migration\_mode` *mode*Enables or disables migration mode.



`snapshot\_blkdev`Snapshot device, using snapshot file as target if provided



`snapshot\_blkdev\_internal`Take an internal snapshot on device if it support



`snapshot\_delete\_blkdev\_internal`Delete an internal snapshot on device if it support



`drive\_mirror`Start mirroring a block device’s writes to a new destination,
using the specified target.



`drive\_backup`Start a point-in-time copy of a block device to a specified target.



`drive\_add`Add drive to PCI storage controller.



`pcie\_aer\_inject\_error`Inject PCIe AER error



`netdev\_add`Add host network device.



`netdev\_del`Remove host network device.



`object\_add`Create QOM object.



`object\_del`Destroy QOM object.



`hostfwd\_add`Redirect TCP or UDP connections from host to guest (requires -net user).



`hostfwd\_remove`Remove host-to-guest TCP or UDP redirection.



`balloon` *value*Request VM to change its memory allocation to *value* (in MB).



`set\_link` *name* `[on|off]`Switch link *name* on (i.e. up) or off (i.e. down).



`watchdog\_action`Change watchdog action.



`nbd\_server\_start` *host*:*port*Start an NBD server on the given host and/or port. If the `-a`
option is included, all of the virtual machine’s block devices that
have an inserted media on them are automatically exported; in this case,
the `-w` option makes the devices writable too.



`nbd\_server\_add` *device* [ *name* ]Export a block device through QEMU’s NBD server, which must be started
beforehand with `nbd\_server\_start`. The `-w` option makes the
exported device writable too. The export name is controlled by *name*,
defaulting to *device*.



`nbd\_server\_remove [-f]` *name*Stop exporting a block device through QEMU’s NBD server, which was
previously started with `nbd\_server\_add`. The `-f`
option forces the server to drop the export immediately even if
clients are connected; otherwise the command fails unless there are no
clients.



`nbd\_server\_stop`Stop the QEMU embedded NBD server.



`mce` *cpu* *bank* *status* *mcgstatus* *addr* *misc*Inject an MCE on the given CPU (x86 only).



`getfd` *fdname*If a file descriptor is passed alongside this command using the SCM\_RIGHTS
mechanism on unix sockets, it is stored using the name *fdname* for
later use by other monitor commands.



`closefd` *fdname*Close the file descriptor previously assigned to *fdname* using the
`getfd` command. This is only needed if the file descriptor was never
used by another monitor command.



`block\_set\_io\_throttle` *device* *bps* *bps\_rd* *bps\_wr* *iops* *iops\_rd* *iops\_wr*Change I/O throttle limits for a block drive to
*bps* *bps\_rd* *bps\_wr* *iops* *iops\_rd* *iops\_wr*.
*device* can be a block device name, a qdev ID or a QOM path.



`set\_password [ vnc | spice ] password [ -d display ] [ action-if-connected ]`Change spice/vnc password. *display* can be used with ‘vnc’ to specify
which display to set the password on. *action-if-connected* specifies
what should happen in case a connection is established: *fail* makes
the password change fail. *disconnect* changes the password and
disconnects the client. *keep* changes the password and keeps the
connection up. *keep* is the default.



`expire\_password [ vnc | spice ] expire-time [ -d display ]`Specify when a password for spice/vnc becomes invalid.
*display* behaves the same as in `set\_password`.
*expire-time* accepts:



`now`Invalidate password instantly.



`never`Password stays valid forever.



`+`*nsec*Password stays valid for *nsec* seconds starting now.



*nsec*Password is invalidated at the given time. *nsec* are the seconds
passed since 1970, i.e. unix epoch.





`chardev-add` *args*chardev-add accepts the same parameters as the -chardev command line switch.



`chardev-change` *args*chardev-change accepts existing chardev *id* and then the same arguments
as the -chardev command line switch (except for “id”).



`chardev-remove` *id*Removes the chardev *id*.



`chardev-send-break` *id*Send a break on the chardev *id*.



`qemu-io` *device* *command*Executes a qemu-io command on the given block device.



`qom-list` [*path*]Print QOM properties of object at location *path*



`qom-get` *path* *property*Print QOM property *property* of object at location *path*



`qom-set` *path* *property* *value*Set QOM property *property* of object at location *path* to value *value*



`replay\_break` *icount*Set replay breakpoint at instruction count *icount*.
Execution stops when the specified instruction is reached.
There can be at most one breakpoint. When breakpoint is set, any prior
one is removed. The breakpoint may be set only in replay mode and only
“in the future”, i.e. at instruction counts greater than the current one.
The current instruction count can be observed with `info replay`.



`replay\_delete\_break`Remove replay breakpoint which was previously set with `replay\_break`.
The command is ignored when there are no replay breakpoints.



`replay\_seek` *icount*Automatically proceed to the instruction count *icount*, when
replaying the execution. The command automatically loads nearest
snapshot and replays the execution to find the desired instruction.
When there is no preceding snapshot or the execution is not replayed,
then the command fails.
*icount* for the reference may be observed with `info replay` command.



`calc\_dirty\_rate` *second*Start a round of dirty rate measurement with the period specified in *second*.
The result of the dirty rate measurement may be observed with `info
dirty\_rate` command.



`set\_vcpu\_dirty\_limit`Set dirty page rate limit on virtual CPU, the information about all the
virtual CPU dirty limit status can be observed with `info vcpu\_dirty\_limit`
command.



`cancel\_vcpu\_dirty\_limit`Cancel dirty page rate limit on virtual CPU, the information about all the
virtual CPU dirty limit status can be observed with `info vcpu\_dirty\_limit`
command.



`dumpdtb` *filename*Dump the FDT in dtb format to *filename*.



`xen-event-inject` *port*Notify guest via event channel on port *port*.



`xen-event-list`List event channels in the guest





`info` *subcommand*Show various information about the system state.



`info version`Show the version of QEMU.



`info network`Show the network state.



`info chardev`Show the character devices.



`info block`Show info of one block device or all block devices.



`info blockstats`Show block device statistics.



`info block-jobs`Show progress of ongoing block device operations.



`info registers`Show the cpu registers.



`info lapic`Show local APIC state



`info cpus`Show infos for each CPU.



`info history`Show the command line history.



`info irq`Show the interrupts statistics (if available).



`info pic`Show PIC state.



`info rdma`Show RDMA state.



`info pci`Show PCI information.



`info tlb`Show virtual to physical memory mappings.



`info mem`Show the active virtual memory mappings.



`info mtree`Show memory tree.



`info jit`Show dynamic compiler info.



`info opcount`Show dynamic compiler opcode counters



`info sync-profile [-m|-n]` [*max*]Show synchronization profiling info, up to *max* entries (default: 10),
sorted by total wait time.



`-m`sort by mean wait time



`-n`do not coalesce objects with the same call site




When different objects that share the same call site are coalesced,
the “Object” field shows—enclosed in brackets—the number of objects
being coalesced.



`info kvm`Show KVM information.



`info numa`Show NUMA information.



`info usb`Show guest USB devices.



`info usbhost`Show host USB devices.



`info capture`Show capture information.



`info snapshots`Show the currently saved VM snapshots.



`info status`Show the current VM status (running|paused).



`info mice`Show which guest mouse is receiving events.



`info vnc`Show the vnc server status.



`info spice`Show the spice server status.



`info name`Show the current VM name.



`info uuid`Show the current VM UUID.



`info usernet`Show user network stack connection states.



`info migrate`Show migration status.



`info migrate\_capabilities`Show current migration capabilities.



`info migrate\_parameters`Show current migration parameters.



`info balloon`Show balloon information.



`info qtree`Show device tree.



`info qdm`Show qdev device model list.



`info qom-tree`Show QOM composition tree.



`info roms`Show roms.



`info trace-events`Show available trace-events & their state.



`info tpm`Show the TPM device.



`info memdev`Show memory backends



`info memory-devices`Show memory devices.



`info iothreads`Show iothread’s identifiers.



`info rocker` *name*Show rocker switch.



`info rocker-ports` *name*-portsShow rocker ports.



`info rocker-of-dpa-flows` *name* [*tbl\_id*]Show rocker OF-DPA flow tables.



`info rocker-of-dpa-groups` *name* [*type*]Show rocker OF-DPA groups.



`info skeys` *address*Display the value of a storage key (s390 only)



`info cmma` *address*Display the values of the CMMA storage attributes for a range of
pages (s390 only)



`info dump`Display the latest dump status.



`info ramblock`Dump all the ramblocks of the system.



`info hotpluggable-cpus`Show information about hotpluggable CPUs



`info vm-generation-id`Show Virtual Machine Generation ID



`info memory\_size\_summary`Display the amount of initially allocated and present hotpluggable (if
enabled) memory in bytes.



`info sev`Show SEV information.



`info replay`Display the record/replay information: mode and the current icount.



`info dirty\_rate`Display the vcpu dirty rate information.



`info vcpu\_dirty\_limit`Display the vcpu dirty page limit information.



`info sgx`Show intel SGX information.



`info via`Show guest mos6522 VIA devices.



`stats`Show runtime-collected statistics



`info virtio`List all available virtio devices



`info virtio-status` *path*Display status of a given virtio device



`info virtio-queue-status` *path* *queue*Display status of a given virtio queue



`info virtio-vhost-queue-status` *path* *queue*Display status of a given vhost queue



`info virtio-queue-element` *path* *queue* [*index*]Display element of a given virtio queue



`info cryptodev`Show the crypto devices.








Integer expressions[¶](#integer-expressions "Permalink to this headline")
-------------------------------------------------------------------------


The monitor understands integers expressions for every integer argument.
You can use register names to get the value of specifics CPU registers
by prefixing them with *$*.
