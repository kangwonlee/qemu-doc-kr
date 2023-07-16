# Introduction<br>소개

## Virtualisation Accelerators<br>가상화 가속기

QEMU\'s system emulation provides a virtual model of a machine (CPU,
memory and emulated devices) to run a guest OS. It supports a number of
hypervisors (known as accelerators) as well as a JIT known as the Tiny
Code Generator (TCG) capable of emulating many CPUs.<br>
QEMU의 시스템 emulation 은 어떤 컴퓨터(CPU, 메모리, 에뮬레이트 되는 디바이스)의 가상 모델을 제공하여 게스트 OS를 실행시킵니다. 이는 여러 하이퍼바이저 (가속기라고도 합니다)와 많은 CPU를 에뮬레이크 할 수 있는 JIT (TCG Tiny Code Generator 작은 코드 생성기) 를 지원합니다.

Supported Accelerators<br>지원되는 가속기
  Accelerator<br>가속기                    |   Host OS<br>호스트 OS               |               Host Architectures<br>호스트 아키텍춰
  ---------------------------------------|------------------------------------ |--------------------------------------------------
  KVM                                    | Linux                               | Arm (64 bit only), MIPS, PPC, RISC-V, s390x, x86
  Xen                                    | Linux (as dom0)                     | Arm, x86
  Intel HAXM (hax)                       | Linux, Windows                      | x86
  Hypervisor Framework (hvf)             | MacOS                               | x86 (64 bit only), Arm (64 bit only)
  Windows Hypervisor Platform (whpx)     | Windows                             | x86
  NetBSD Virtual Machine Monitor (nvmm)  | NetBSD                              | x86
  Tiny Code Generator (tcg)              | Linux, other POSIX, Windows, MacOS  | Arm, x86, Loongarch64, MIPS, PPC, s390x, Sparc64


## Feature Overview<br>기능 요약

System emulation provides a wide range of device models to emulate
various hardware components you may want to add to your machine. This
includes a wide number of VirtIO devices which are specifically tuned
for efficient operation under virtualisation. Some of the device
emulation can be offloaded from the main QEMU process using either
vhost-user (for VirtIO) or [`Multi-process QEMU`](multi-process.md). If the platform supports it QEMU also supports directly
passing devices through to guest VMs to eliminate the device emulation
overhead. See [`device-emulation`](device-emulation.md) for more
details.

시스템 에뮬레이션은 너른 범위의 디바이스 모델을 제공, 여러분들이 여러분들의 컴퓨터에 추가 하고 싶을 다양한 하드웨어 요소를 에뮬레이트 합니다.  이는 방대한 VirtIO 가상입출력 장치를 포함합니다. 이는 특히 가상화 환경에서 효율적인 작동을 위해 튜닝되었습니다. 디바이스 에뮬레이션의 일부는 메인 QEMU 프로세스에서 대신 vhost-user (VirtIO) 또는 [`Multi-process QEMU`](multi-process.md) 를 사용할 수 있습니다. 플랫폼이 지원하는 경우, QEMU는 또한 디바이스를 직접 게스트 VM에 직접 연결하는 것도 지원합니다.  이렇게 하면 디바이스 에뮬레이션 오버헤드를 없앨 수 있습니다. 자세한 내용은 [`device-emulation`](device-emulation.md) 을 참조하십시오.

There is a full
[`Live Block Operations`](../interop/live-block-operations.md) which allows for construction of complex storage topology
which can be stacked across multiple layers supporting redirection,
networking, snapshots and migration support.

[`Live Block Operations`](../interop/live-block-operations.md) 로 여러 계층에 걸쳐 복잡한 저장소 연결 구조를 구성할 수 있습니다. 이러한  계층 구조는 리다이렉션, 네트워킹, 스냅샷 및 마이그레이션 지원을 지원합니다.

The flexible `chardev` system allows for handling IO from character like
devices using stdio, files, unix sockets and TCP networking.

`chardev` 시스템은 표준입출력, 파일, 유닉스 소켓과 TCP 네트워킹을 사용하여 문자와 비슷한 다양한 입출력을 다룰 수 있는 유연성을 갖추고 있습니다.

QEMU provides a number of management interfaces including a line based
[`Human Monitor Protocol (HMP)`](monitor.md) that allows you to dynamically add and remove devices as
well as introspect the system state. The
[`QEMU Monitor Protocol`](../interop/qemu-qmp-ref.md) is
a well defined, versioned, machine usable API that presents a rich
interface to other tools to create, control and manage Virtual Machines.
This is the interface used by higher level tools interfaces such as
[Virt Manager](https://virt-manager.org/) using the [libvirt
framework](https://libvirt.org).

QEMU 는 몇가지 관리 인터페이스를 갖추고 있습니다. (명령)행 기반 [`Human Monitor Protocol (HMP)`](monitor.md) 은 동적으로 디바이스를 추가, 제거하거나 조사해 볼 수 있습니다.
[`QEMU Monitor Protocol`](../interop/qemu-qmp-ref.md) 는 명확하게 정의되어 있고, 버전 관리되고 있으며, 자동화 가능한 API 로 VM을 생성, 제어, 관리하는 다른 소프트웨어 도구에 다양한 기능을 제공할 수 있습니다. 이 인터페이스는 [libvirt
framework](https://libvirt.org)를 사용하는 [Virt Manager](https://virt-manager.org/) 같은 더 높은 수준의 인터페이스도 사용합니다.

For the common accelerators QEMU, supported debugging with its
[`gdbstub`](gdb.md) which allows users to
connect GDB and debug system software images.

일반적인 가속기에 대해 QEMU 는 [`gdbstub`](gdb.md) 을 지원합니다. 이를 사용하여 사용자가 시스템 소프트웨어 이미지를 GDB와 연결하고 디버깅 할 수 있습니다.

## Running<br>실행

QEMU provides a rich and complex API which can be overwhelming to
understand. While some architectures can boot something with just a disk
image, those examples elide a lot of details with defaults that may not
be optimal for modern systems.

QEMU 의 API 는 다양한 기능을 제공하지만 복잡하고 이해하기 어려울 수 있습니다. 일부 아키텍처는 디스크 이미지만으로도 부팅할 수 있지만, 이러한 예제는 다수의 세부 설정에 기본값을 사용할 것이지만 현대 시스템에 최적은 아닐 수 있습니다.

For a non-x86 system where we emulate a broad range of machine types,
the command lines are generally more explicit in defining the machine
and boot behaviour. You will often find example command lines in
the [`system-targets-ref`](targets.md) section of the
manual.

방대한 형태의 시스템을 에뮬레이트하게 되는 비 x86 시스템의 경우, 일반적으로 명령행이 더  명시적으로 해당 시스템과 부팅 동작을 정의합니다. 매뉴얼의 [`system-targets-ref`](targets.md) 섹션에서 명령행 예제를 찾을 수 있습니다.

While the project doesn\'t want to discourage users from using the
command line to launch VMs, we do want to highlight that there are a
number of projects dedicated to providing a more user friendly
experience. Those built around the `libvirt` framework can make use of
feature probing to build modern VM images tailored to run on the
hardware you have.

사용자가 명령행을 사용하여 VM을 시작하는 것도 괜찮지만, QEMU 개발진은 더 사용자 친화적인 방식을 제공하고자 한다는 점을 강조하고 싶습니다. `libvirt` 프레임워크를 기반으로 한 여러 프로젝트는 사용자가 보유한 하드웨어 상에서 실행되도록 맞춤 설정된 현대적인 VM 이미지를 만들기 위한 실험적인 기능을 사용합니다.

That said, the general form of a QEMU command line can be expressed as:

```
$ qemu-system-x86_64 [machine opts] \
                [cpu opts] \
                [accelerator opts] \
                [device opts] \
                [backend opts] \
                [interface opts] \
                [boot opts]
```

Most options will generate some help information. So for example:
대부분의 옵션은 도움말을 생상할 수 있습니다. 예를 들어:

```
$ qemu-system-x86_64 -M help
```

will list the machine types supported by that QEMU binary. `help` can
also be passed as an argument to another option. For example:

위 명령은 QEMU 실행파일이 지원하는 시스템 종류를 나열할 것입니다. `help` 는 다른 옵션의 인자로도 사용할 수 있습니다. 예를 들어:

```
$ qemu-system-x86_64 -device scsi-hd,help
```

will list the arguments and their default values of additional options
that can control the behaviour of the `scsi-hd` device.

위 명령은 `scsi-hd` 디바이스의 동작을 제어할 수 있는 추가 옵션의 매개변수와 기본값을 나열합니다.

### Options Overview
| Option<br>옵션 | Description<br>설명 |
| --- | --- |
| Machine | Define the machine type, amount of memory etc<br>시스템 타입, 메모리 크기 등을 정의 |
| CPU | Type and number/topology of vCPUs. Most accelerators offer a `host` cpu option which simply passes through your host CPU configuration without filtering out any features.<br>가상 CPU 의 종류와 수, 연결구조. 대부분의 가속기는 `host` cpu 옵션을 제공함. 단순히 host CPU 와 연결시키며 다른 기능을 배제함. |
| Accelerator | This will depend on the hypervisor you run. Note that the default is TCG, which is purely emulated, so you must specify an accelerator type to take advantage of hardware virtualization.<br>실행 하이퍼바이저에 따라 달라짐. 기본은 TCG 로 에뮬레이션만 함. 따라서 하드웨어 가상화의 혜택을 누리려면 가속기 형태를 지정해야 함.  |
| Devices | Additional devices that are not defined by default with the machine type.<br>시스템 타입의 기본에 정의되지 않은 추가 디바이스 |
| Backends | Backends are how QEMU deals with the guest\'s data, for example how a block device is stored, how network devices see the network or how a serial device is directed to the outside world.<br>백엔드는 QEMU가 어떻게 guest 의 데이터를 다루는가에 관한 것임. 예를 들어 블럭 디바이스가 어떻게 저장되는가? 어떻게 네트워크 디바이스가 네트워크를 보는가? 또는 시리얼 디바이스가 외부 세계로 어떻게 연결되는가? |
| Interfaces | How the system is displayed, how it is managed and controlled or debugged.<br>해당 시스템이 어떻게 표시되는가? 어떻게 관리되고 제어되거나 디버그 되는가? |
| Boot | How the system boots, via firmware or direct kernel boot.<br>어떻게 시스템이 부팅하는가? 펌웨어를 통해서? 아니면 커널 부트를 통하는가? |

In the following example we first define a `virt` machine which is a
general purpose platform for running Aarch64 guests. We enable
virtualisation so we can use KVM inside the emulated guest. As the
`virt` machine comes with some built in pflash devices we give them
names so we can override the defaults later.

아래 사례에서는 먼저 `virt` 머신을 정의할 것입니다. 이것은 Aarch 64 게스트를 실행하기 위한 범용 플랫폼입니다. 가상화를 활성화하여 에뮬레이트된 게스트 안에서 KVM 을 사용할 수 있게 할 것입니다. `virt` 머신은 내장된 pflash 디바이스를 가지고 있으므로 기본값을 나중에 오버라이드할 수 있도록 이름을 지정합니다.

``` 
$ qemu-system-aarch64 \
   -machine type=virt,virtualization=on,pflash0=rom,pflash1=efivars \
   -m 4096 \
```

We then define the 4 vCPUs using the `max` option which gives us all the
Arm features QEMU is capable of emulating. We enable a more emulation
friendly implementation of Arm\'s pointer authentication algorithm. We
explicitly specify TCG acceleration even though QEMU would default to it
anyway.

다음으로, 4개의 vCPU 를 정의합니다. `max` 옵션은 QEMU 가 에뮬레이션할 수 있는 모든 Arm 기능을 제공합니다. 구현된 Arm 의 포인터 인증 알고리즘 가운데 에뮬레이션에 더 친숙한 쪽을 활성화합니다. QEMU 는 기본적으로 TCG 가속을 사용하지만 명시적으로 지정합니다.

``` 
-cpu max,pauth-impdef=on \
-smp 4 \
-accel tcg \
```

As the `virt` platform doesn\'t have any default network or storage
devices we need to define them. We give them ids so we can link them
with the backend later on.

``` 
-device virtio-net-pci,netdev=unet \
-device virtio-scsi-pci \
-device scsi-hd,drive=hd \
```

We connect the user-mode networking to our network device. As user-mode
networking isn\'t directly accessible from the outside world we forward
localhost port 2222 to the ssh port on the guest.

``` 
-netdev user,id=unet,hostfwd=tcp::2222-:22 \
```

We connect the guest visible block device to an LVM partition we have
set aside for our guest.

``` 
-blockdev driver=raw,node-name=hd,file.driver=host_device,file.filename=/dev/lvm-disk/debian-bullseye-arm64 \
```

We then tell QEMU to multiplex the `QEMU monitor`{.interpreted-text
role="ref"} with the serial port output (we can switch between the two
using `keys in the
character backend multiplexer`{.interpreted-text role="ref"}). As there
is no default graphical device we disable the display as we can work
entirely in the terminal.

``` 
-serial mon:stdio \
-display none \
```

Finally we override the default firmware to ensure we have some storage
for EFI to persist its configuration. That firmware is responsible for
finding the disk, booting grub and eventually running our system.

``` 
-blockdev node-name=rom,driver=file,filename=(pwd)/pc-bios/edk2-aarch64-code.fd,read-only=true \
-blockdev node-name=efivars,driver=file,filename=$HOME/images/qemu-arm64-efivars
```
