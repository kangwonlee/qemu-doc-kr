# Device Emulation<br>디바이스 에뮬레이션

QEMU supports the emulation of a large number of devices from
peripherals such network cards and USB devices to integrated systems on
a chip (SoCs). Configuration of these is often a source of confusion so
it helps to have an understanding of some of the terms used to describes
devices within QEMU.

QEMU 는 네트워크 카드, USB 장치 같은 주변기기로부터 시스템 온 칩 SoC 에 이르는 다수의 디바이스의 에뮬레이션을 지원합니다. 이들의 설정은 때로 혼란을 불러일으킬 수 있으므로 QEMU 내에서 디바이스를 설명하는데 사용되는 몇 가지 용어에 대한 이해를 가지고 있으면 도움이 됩니다.

## Common Terms<br>
공통 용어

### Device Front End<br>
장치 프론트엔드

A device front end is how a device is presented to the guest. The type
of device presented should match the hardware that the guest operating
system is expecting to see. All devices can be specified with the
`--device` command line option. Running QEMU with the command line
options `--device help` will list all devices it is aware of. Using the
command line `--device foo,help` will list the additional configuration
options available for that device.

디바이스 프론트엔드는 어떤 장치가 어떻게 게스트에게 보이는가 하는 것이다. 제공되는 장치의 타입은 게스트 운영체제가 보게 될 것으로 예상하는 하드웨어와 일치해야 한다. 모든 장치는 `--device` 커맨드라인 옵션으로 지정할 수 있다. QEMU 를 실행하면서 `--device help` 명령행 옵션을 사용하면 알고 있는 모든 장치를 나열할 것이다. `--device foo,help` 커맨드라인 옵션을 사용하면 해당 장치에 대한 추가적인 설정 옵션을 나열한다.

A front end is often paired with a back end, which describes how the
host\'s resources are used in the emulation.

어떤 프론트엔드는 때로 어떤 백엔드와 쌍을 이루는데, 이는 호스트의 리소스가 에뮬레이션에서 어떻게 사용되는지를 묘사한다.

### Device Buses<br>
디바이스 버스

Most devices will exist on a BUS of some sort. Depending on the machine
model you choose (`-M foo`) a number of buses will have been
automatically created. In most cases the BUS a device is attached to can
be inferred, for example PCI devices are generally automatically
allocated to the next free address of first PCI bus found. However in
complicated configurations you can explicitly specify what bus
(`bus=ID`) a device is attached to along with its address (`addr=N`).

대부분의 장치는 어떤 종류의 버스 위에 존재합니다. 선택한 머신 모델에 따라 (`-M foo`) 일부 버스가 자동으로 생성됩니다. 대부분의 경우 장치가 연결된 버스는 추론될 수 있습니다. 예를 들어 PCI 장치는 일반적으로 찾은 첫 번째 빈 PCI 버스의 다음 주소에 자동으로 할당됩니다. 그러나 복잡한 구성에서는 장치가 연결된 버스 (`bus=ID`)와 주소 (`addr=N`)를 명시적으로 지정할 수 있습니다.

Some devices, for example a PCI SCSI host controller, will add an
additional buses to the system that other devices can be attached to. A
hypothetical chain of devices might look like:

어떤 장치는, 예를 들어 PCI SCSI 호스트 콘트롤러 같은 경우, 다른 장치가 붙을 수 있는 또 다른 버스를 시스템에 추가할 수 있습니다. 가상의 장치 체인은 다음과 비슷해 보일 수 있습니다.

```
--device foo,bus=pci.0,addr=0,id=foo --device bar,bus=foo.0,addr=1,id=baz
```

which would be a `bar` device (with the ID of baz) which is attached to
the first `foo` bus (`foo.0`) at address 1. The `foo` device which provides
that bus is itself is attached to the first PCI bus (`pci.0`).

`bar` 장치 (ID 가 baz 인)는 첫 번째 `foo` 버스 (`foo.0`)에 주소 1로 연결될 것입니다. 그 버스를 제공하는 `foo` 장치는 첫 번째 PCI 버스 (`pci.0`)에 연결됩니다.

### Device Back End

The back end describes how the data from the emulated device will be
processed by QEMU. The configuration of the back end is usually specific
to the class of device being emulated. For example serial devices will
be backed by a `--chardev` which can redirect the data to a file or
socket or some other system. Storage devices are handled by `--blockdev`
which will specify how blocks are handled, for example being stored in a
qcow2 file or accessing a raw host disk partition. Back ends can
sometimes be stacked to implement features like snapshots.

While the choice of back end is generally transparent to the guest,
there are cases where features will not be reported to the guest if the
back end is unable to support it.

### Device Pass Through

Device pass through is where the device is actually given access to the
underlying hardware. This can be as simple as exposing a single USB
device on the host system to the guest or dedicating a video card in a
PCI slot to the exclusive use of the guest.

## Emulated Devices

* [CAN Bus Emulation Support](devices/can.rst)
* [Chip Card Interface Device (CCID)](devices/ccid.rst)
* [Compute Express Link (CXL)](devices/cxl.rst)
* [Inter-VM Shared Memory device](devices/ivshmem.rst)
* [Sparc32 keyboard](devices/keyboard.rst)
* [Network emulation](devices/net.rst)
* [NVMe Emulation](devices/nvme.rst)
* [USB emulation](devices/usb.rst)
* [vhost-user back ends](devices/vhost-user.rst)
* [virtio pmem](devices/virtio-pmem.rst)
* [QEMU vhost-user-rng - RNG emulation](devices/vhost-user-rng.rst)
* [CanoKey QEMU](devices/canokey.rst)
* [Universal Second Factor (U2F) USB Key Device](devices/usb-u2f.rst)
* [igb](devices/igb.rst)
