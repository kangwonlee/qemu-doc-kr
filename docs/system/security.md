# Security<br>보안

## Overview<br>개요

This chapter explains the security requirements that QEMU is designed to
meet and principles for securely deploying QEMU.<br>
이 장은 QEMU가 충족시키도록 설계된 보안 요구 사항과 QEMU를 안전하게 배포하는 원칙을 설명합니다.

## Security Requirements<br>보안 요구 사항

QEMU supports many different use cases, some of which have stricter
security requirements than others. The community has agreed on the
overall security requirements that users may depend on. These
requirements define what is considered supported from a security
perspective.<br>
QEMU는 많은 다양한 유즈케이스를 지원하며, 일부는 다른 것보다 엄격한 보안 요구 사항을 가지고 있습니다. 커뮤니티는 사용자가 의존할 수 있는 전반적인 보안 요구 사항에 동의했습니다. 이러한 요구 사항은 보안 관점에서 어떤 것이 지원되는 것으로 간주되는지를 정의합니다.

### Virtualization Use Case<br>가상화 유즈케이스

The virtualization use case covers cloud and virtual private server
(VPS) hosting, as well as traditional data center and desktop
virtualization. These use cases rely on hardware virtualization
extensions to execute guest code safely on the physical CPU at
close-to-native speed.<br>
가상화 유즈케이스는 클라우드 및 가상 사설 서버(VPS) 호스팅, 전통적인 데이터 센터 와 데스크톱 가상화를 포함합니다. 이러한 유즈케이스에서 물리적 CPU가 게스트 코드를 안전하게 거의 네이티브 속도로 실행할 수 있는지 여부는 하드웨어 가상화 확장에 달려 있습니다.

The following entities are untrusted, meaning that they may be buggy or
malicious:<br>
다음 entity는 신뢰할 수 없으며, 즉 버그가 있거나 악의적일 수 있습니다:

-   Guest<br>게스트
-   User-facing interfaces (e.g. VNC, SPICE, WebSocket)<br>사용자 인터페이스 (예: VNC, SPICE, WebSocket)
-   Network protocols (e.g. NBD, live migration)<br>네트워크 프로토콜 (예: NBD, 라이브 마이그레이션)
-   User-supplied files (e.g. disk images, kernels, device trees)<br>사용자 제공 파일 (예: 디스크 이미지, 커널, 디바이스 트리)
-   Passthrough devices (e.g. PCI, USB)<br>패스스루 장치 (예: PCI, USB)

Bugs affecting these entities are evaluated on whether they can cause
damage in real-world use cases and treated as security bugs if this is
the case.<br>
이러한 entity에 영향을 미치는 버그는 실제 유즈케이스에서 손상을 일으킬 수 있는지 여부로 평가되며, 이 경우 보안 버그로 처리됩니다.

### Non-virtualization Use Case<br>비 가상화 유즈케이스

The non-virtualization use case covers emulation using the Tiny Code
Generator (TCG). In principle the TCG and device emulation code used in
conjunction with the non-virtualization use case should meet the same
security requirements as the virtualization use case. However, for
historical reasons much of the non-virtualization use case code was not
written with these security requirements in mind.<br>
비 가상화 유즈케이스는 Tiny Code Generator (TCG)를 사용한 에뮬레이션을 다룹니다. 원칙적으로 비 가상화 유즈케이스와 함께 사용되는 TCG 와 디바이스 에뮬레이션 코드는 가상화 유즈케이스와 동일한 보안 요구 사항을 충족해야 합니다. 그러나 역사적인 이유로 비 가상화 유즈케이스 코드의 대부분은 이러한 보안 요구 사항을 생각하지 않고 작성되었습니다.

Bugs affecting the non-virtualization use case are not considered
security bugs at this time. Users with non-virtualization use cases must
not rely on QEMU to provide guest isolation or any security guarantees.<br>이러한 비 가상화 유즈케이스에 영향을 끼치는 버그는 현재는 보안 버그로 간주되지 않습니다. 비 가상화 유즈케이스를 사용하는 사용자는 게스트 격리 또는 보안 보장 제공을 QEMU에만 의존해서는 안 됩니다.

## Architecture<br>아키텍처

This section describes the design principles that ensure the security
requirements are met.<br>
이 섹션은 보안 요구 사항을 충족되는지 확인하는 설계 원칙을 설명합니다.

### Guest Isolation<br>게스트 분리

Guest isolation is the confinement of guest code to the virtual machine.
When guest code gains control of execution on the host this is called
escaping the virtual machine. Isolation also includes resource limits
such as throttling of CPU, memory, disk, or network. Guests must be
unable to exceed their resource limits.<br>
게스트 분리는 게스트 코드를 가상 머신 내에서만 실행되도록 하는 것입니다. 게스트 코드가 호스트에서 실행 제어권을 얻으면 이를 가상 머신에서 탈출한다고 합니다. 격리에는 또한 CPU, 메모리, 디스크 또는 네트워크의 제한과 같은 리소스 제한도 포함됩니다. 게스트는 리소스 제한을 초과할 수 없어야 합니다.

QEMU presents an attack surface to the guest in the form of emulated
devices. The guest must not be able to gain control of QEMU. Bugs in
emulated devices could allow malicious guests to gain code execution in
QEMU. At this point the guest has escaped the virtual machine and is
able to act in the context of the QEMU process on the host.<br>
QEMU 는 에뮬레이션된 장치 형태로 게스트에게 공격 표면을 제공합니다. 게스트는 QEMU의 제어권을 얻을 수 없어야 합니다. 에뮬레이션된 장치의 버그로 인해 악의적인 게스트 코드가 QEMU 안에서 실행될 수도 있습니다. 이 시점에서 게스트는 가상 머신에서 탈출한 것이고 호스트의 QEMU 프로세스의 컨텍스트에서 작동할 수 있습니다.

Guests often interact with other guests and share resources with them. A
malicious guest must not gain control of other guests or access their
data. Disk image files and network traffic must be protected from other
guests unless explicitly shared between them by the user.<br>
게스트는 때로 다른 게스트와 상호 작용하고 리소스를 공유합니다. 악의적인 게스트가 다른 게스트의 제어권을 갖게 되거나 데이터에 접근할 수 없어야 합니다. 디스크 이미지 파일과 네트워크 트래픽은 사용자가 명시적으로 공유하지 않는 한 다른 게스트로부터 보호되어야 합니다.

### Principle of Least Privilege

The principle of least privilege states that each component only has
access to the privileges necessary for its function. In the case of QEMU
this means that each process only has access to resources belonging to
the guest.

The QEMU process should not have access to any resources that are
inaccessible to the guest. This way the guest does not gain anything by
escaping into the QEMU process since it already has access to those same
resources from within the guest.

Following the principle of least privilege immediately fulfills guest
isolation requirements. For example, guest A only has access to its own
disk image file `a.img` and not guest B\'s disk image file `b.img`.

In reality certain resources are inaccessible to the guest but must be
available to QEMU to perform its function. For example, host system
calls are necessary for QEMU but are not exposed to guests. A guest that
escapes into the QEMU process can then begin invoking host system calls.

New features must be designed to follow the principle of least
privilege. Should this not be possible for technical reasons, the
security risk must be clearly documented so users are aware of the
trade-off of enabling the feature.

### Isolation mechanisms

Several isolation mechanisms are available to realize this architecture
of guest isolation and the principle of least privilege. With the
exception of Linux seccomp, these mechanisms are all deployed by
management tools that launch QEMU, such as libvirt. They are also
platform-specific so they are only described briefly for Linux here.

The fundamental isolation mechanism is that QEMU processes must run as
unprivileged users. Sometimes it seems more convenient to launch QEMU as
root to give it access to host devices (e.g. `/dev/net/tun`) but this
poses a huge security risk. File descriptor passing can be used to give
an otherwise unprivileged QEMU process access to host devices without
running QEMU as root. It is also possible to launch QEMU as a non-root
user and configure UNIX groups for access to `/dev/kvm`, `/dev/net/tun`,
and other device nodes. Some Linux distros already ship with UNIX groups
for these devices by default.

-   SELinux and AppArmor make it possible to confine processes beyond
    the traditional UNIX process and file permissions model. They
    restrict the QEMU process from accessing processes and files on the
    host system that are not needed by QEMU.
-   Resource limits and cgroup controllers provide throughput and
    utilization limits on key resources such as CPU time, memory, and
    I/O bandwidth.
-   Linux namespaces can be used to make process, file system, and other
    system resources unavailable to QEMU. A namespaced QEMU process is
    restricted to only those resources that were granted to it.
-   Linux seccomp is available via the QEMU `--sandbox` option. It
    disables system calls that are not needed by QEMU, thereby reducing
    the host kernel attack surface.

## Sensitive configurations

There are aspects of QEMU that can have security implications which
users & management applications must be aware of.

### Monitor console (QMP and HMP)

The monitor console (whether used with QMP or HMP) provides an interface
to dynamically control many aspects of QEMU\'s runtime operation. Many
of the commands exposed will instruct QEMU to access content on the host
file system and/or trigger spawning of external processes.

For example, the `migrate` command allows for the spawning of arbitrary
processes for the purpose of tunnelling the migration data stream. The
`blockdev-add` command instructs QEMU to open arbitrary files, exposing
their content to the guest as a virtual disk.

Unless QEMU is otherwise confined using technologies such as SELinux,
AppArmor, or Linux namespaces, the monitor console should be considered
to have privileges equivalent to those of the user account QEMU is
running under.

It is further important to consider the security of the character device
backend over which the monitor console is exposed. It needs to have
protection against malicious third parties which might try to make
unauthorized connections, or perform man-in-the-middle attacks. Many of
the character device backends do not satisfy this requirement and so
must not be used for the monitor console.

The general recommendation is that the monitor console should be exposed
over a UNIX domain socket backend to the local host only. Use of the TCP
based character device backend is inappropriate unless configured to use
both TLS encryption and authorization control policy on client
connections.

In summary, the monitor console is considered a privileged control
interface to QEMU and as such should only be made accessible to a
trusted management application or user.
