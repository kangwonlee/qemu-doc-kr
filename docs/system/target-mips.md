# MIPS System emulator {#MIPS-System-emulator}

Four executables cover simulation of 32 and 64-bit MIPS systems in both
endian options, `qemu-system-mips`, `qemu-system-mipsel`
`qemu-system-mips64` and `qemu-system-mips64el`. Five different machine
types are emulated:

-   The MIPS Malta prototype board \"malta\"
-   An ACER Pica \"pica61\". This machine needs the 64-bit emulator.
-   MIPS emulator pseudo board \"mipssim\"
-   A MIPS Magnum R4000 machine \"magnum\". This machine needs the
    64-bit emulator.

The Malta emulation supports the following devices:

-   Core board with MIPS 24Kf CPU and Galileo system controller
-   PIIX4 PCI/USB/SMbus controller
-   The Multi-I/O chip\'s serial device
-   PCI network cards (PCnet32 and others)
-   Malta FPGA serial device
-   Cirrus (default) or any other PCI VGA graphics card

The Boston board emulation supports the following devices:

-   Xilinx FPGA, which includes a PCIe root port and an UART
-   Intel EG20T PCH connects the I/O peripherals, but only the SATA bus
    is emulated

The ACER Pica emulation supports:

-   MIPS R4000 CPU
-   PC-style IRQ and DMA controllers
-   PC Keyboard
-   IDE controller

The MIPS Magnum R4000 emulation supports:

-   MIPS R4000 CPU
-   PC-style IRQ controller
-   PC Keyboard
-   SCSI controller
-   G364 framebuffer

The Fuloong 2E emulation supports:

-   Loongson 2E CPU
-   Bonito64 system controller as North Bridge
-   VT82C686 chipset as South Bridge
-   RTL8139D as a network card chipset

The Loongson-3 virtual platform emulation supports:

-   Loongson 3A CPU
-   LIOINTC as interrupt controller
-   GPEX and virtio as peripheral devices
-   Both KVM and TCG supported

The mipssim pseudo board emulation provides an environment similar to
what the proprietary MIPS emulator uses for running Linux. It supports:

-   A range of MIPS CPUs, default is the 24Kf
-   PC style serial port
-   MIPSnet network emulation

## Supported CPU model configurations on MIPS hosts

QEMU supports variety of MIPS CPU models:

### Supported CPU models for MIPS32 hosts

The following CPU models are supported for use on MIPS32 hosts.
Administrators / applications are recommended to use the CPU model that
matches the generation of the host CPUs in use. In a deployment with a
mixture of host CPU models between machines, if live migration
compatibility is required, use the newest CPU model that is compatible
across all desired hosts.

`mips32r6-generic`

:   MIPS32 Processor (Release 6, 2015)

`P5600`

:   MIPS32 Processor (P5600, 2014)

`M14K`, `M14Kc`

:   MIPS32 Processor (M14K, 2009)

`74Kf`

:   MIPS32 Processor (74K, 2007)

`34Kf`

:   MIPS32 Processor (34K, 2006)

`24Kc`, `24KEc`, `24Kf`

:   MIPS32 Processor (24K, 2003)

`4Kc`, `4Km`, `4KEcR1`, `4KEmR1`, `4KEc`, `4KEm`

:   MIPS32 Processor (4K, 1999)

### Supported CPU models for MIPS64 hosts

The following CPU models are supported for use on MIPS64 hosts.
Administrators / applications are recommended to use the CPU model that
matches the generation of the host CPUs in use. In a deployment with a
mixture of host CPU models between machines, if live migration
compatibility is required, use the newest CPU model that is compatible
across all desired hosts.

`I6400`

:   MIPS64 Processor (Release 6, 2014)

`Loongson-2E`

:   MIPS64 Processor (Loongson 2, 2006)

`Loongson-2F`

:   MIPS64 Processor (Loongson 2, 2008)

`Loongson-3A1000`

:   MIPS64 Processor (Loongson 3, 2010)

`Loongson-3A4000`

:   MIPS64 Processor (Loongson 3, 2018)

`mips64dspr2`

:   MIPS64 Processor (Release 2, 2006)

`MIPS64R2-generic`, `5KEc`, `5KEf`

:   MIPS64 Processor (Release 2, 2002)

`20Kc`

:   MIPS64 Processor (20K, 2000

`5Kc`, `5Kf`

:   MIPS64 Processor (5K, 1999)

`VR5432`

:   MIPS64 Processor (VR, 1998)

`R4000`

:   MIPS64 Processor (MIPS III, 1991)

### Supported CPU models for nanoMIPS hosts

The following CPU models are supported for use on nanoMIPS hosts.
Administrators / applications are recommended to use the CPU model that
matches the generation of the host CPUs in use. In a deployment with a
mixture of host CPU models between machines, if live migration
compatibility is required, use the newest CPU model that is compatible
across all desired hosts.

`I7200`

:   MIPS I7200 (nanoMIPS, 2018)

### Preferred CPU models for MIPS hosts

The following CPU models are preferred for use on different MIPS hosts:

`MIPS III`

:   R4000

`MIPS32R2`

:   34Kf

`MIPS64R6`

:   I6400

`nanoMIPS`

:   I7200

## nanoMIPS System emulator {#nanoMIPS-System-emulator}

Executable `qemu-system-mipsel` also covers simulation of 32-bit
nanoMIPS system in little endian mode:

-   nanoMIPS I7200 CPU

Example of `qemu-system-mipsel` usage for nanoMIPS is shown below:

Download `<disk_image_file>` from
<https://mipsdistros.mips.com/LinuxDistro/nanomips/buildroot/index.html>.

Download `<kernel_image_file>` from
<https://mipsdistros.mips.com/LinuxDistro/nanomips/kernels/v4.15.18-432-gb2eb9a8b07a1-20180627102142/index.html>.

Start system emulation of Malta board with nanoMIPS I7200 CPU:

    qemu-system-mipsel -cpu I7200 -kernel <kernel_image_file> \
        -M malta -serial stdio -m <memory_size> -hda <disk_image_file> \
        -append "mem=256m@0x0 rw console=ttyS0 vga=cirrus vesa=0x111 root=/dev/sda"
