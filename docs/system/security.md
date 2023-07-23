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

### Principle of Least Privilege<br>최소 권한 원칙

The principle of least privilege states that each component only has
access to the privileges necessary for its function. In the case of QEMU
this means that each process only has access to resources belonging to
the guest.<br>
최소 권한 원칙에 따르면 각 요소는 그 기능을 위해 필요한 권한만 가질 수 있습니다. QEMU 의 경우, 이는 각 프로세스가 해당 게스트에 딸린 리소스에만 접근할 수 있다는 의미입니다.

The QEMU process should not have access to any resources that are
inaccessible to the guest. This way the guest does not gain anything by
escaping into the QEMU process since it already has access to those same
resources from within the guest.
QEMU 프로세스는 게스트가 접근할 수 없는 어떠한 리소스에도 접근 권한을 가져서는 안됩니다. 이런 식으로 게스트는 QEMU 프로세스로 탈출하더라도 아무 것도 얻을 수 없습니다. 왜냐하면 게스트 안으로부터 이미 접근할 수 있었던 같은 리소스에만 접근 권한을 가지기 때문입니다.

Following the principle of least privilege immediately fulfills guest
isolation requirements. For example, guest A only has access to its own
disk image file `a.img` and not guest B\'s disk image file `b.img`.<br>
최소 접근 권한 원칙을 따르면 게스트 분리 요구사항도 만족시키게 됩니다. 예를 들어 게스트 A는 자신의 디스크 이미지 파일 `a.img` 에만 접근할 수 있고 게스트 B의 디스크 이미지 파일 `b.img` 에는 접근할 수 없습니다.

In reality certain resources are inaccessible to the guest but must be
available to QEMU to perform its function. For example, host system
calls are necessary for QEMU but are not exposed to guests. A guest that
escapes into the QEMU process can then begin invoking host system calls.<br>
실제로는 게스트에 접근 불가한 어떤 리소스는 QEMU에게는 사용 가능해야 그 기능을 수행할 수 있습니다. 예를 들어, QEMU는 호스트 시스템 함수를 호출할 필요가 있지만 게스트에게 노출되지는 않습니다.

New features must be designed to follow the principle of least
privilege. Should this not be possible for technical reasons, the
security risk must be clearly documented so users are aware of the
trade-off of enabling the feature.<br>
새로운 기능은 반드시 최소 권한 원칙을 따르도록 설계되어야 합니다. 기술적인 이유로 이것이 불가능하다면, 보안 위험은 반드시 명시적으로 문서화되어 사용자들이 해당 기능을 활성화 하는데 댓가를 알 수 있어야 합니다.

### Isolation mechanisms<br>분리 메커니즘

Several isolation mechanisms are available to realize this architecture
of guest isolation and the principle of least privilege. With the
exception of Linux seccomp, these mechanisms are all deployed by
management tools that launch QEMU, such as libvirt. They are also
platform-specific so they are only described briefly for Linux here.<br>
몇가지 분리 메커니즘을 사용하여 게스트 분리 아키텍처와 최소 권한 원칙을 구현할 수 있습니다. Linux seccomp를 제외한 이러한 메커니즘은 libvirt와 같은 QEMU를 실행하는 관리 도구를 통해 제공됩니다. 또한 플랫폼별로 다르기 때문에 여기서는 Linux에 대해서만 간단히 설명합니다.

The fundamental isolation mechanism is that QEMU processes must run as
unprivileged users. Sometimes it seems more convenient to launch QEMU as
root to give it access to host devices (e.g. `/dev/net/tun`) but this
poses a huge security risk. File descriptor passing can be used to give
an otherwise unprivileged QEMU process access to host devices without
running QEMU as root. It is also possible to launch QEMU as a non-root
user and configure UNIX groups for access to `/dev/kvm`, `/dev/net/tun`,
and other device nodes. Some Linux distros already ship with UNIX groups
for these devices by default.<br>
기본적인 분리 메커니즘은 QEMU 프로세스가 비 권한 사용자로 실행되어야 한다는 것입니다. 때로는 호스트 장치 (예: `/dev/net/tun`)에 대한 액세스 권한을 부여하기 위해 QEMU를 root로 실행하는 것이 더 편리해 보이지만 이는 엄청난 보안 위험을 초래합니다. 파일 디스크립터 전달을 사용하면 다른 권한이 높지 않더라도 QEMU 프로세스가 root로 실행되지 않고도 호스트 장치에 액세스할 수 있습니다. 또한 QEMU를 root가 아닌 사용자로 실행하고 UNIX 그룹을 설정하여 `/dev/kvm`, `/dev/net/tun` 와 기타 장치 노드에 대한 접근 권한을 부여할 할 수도 있습니다. 일부 Linux 배포판은 이미 기본적으로 이러한 장치에 대한 UNIX 그룹을 함께 제공합니다.

-   SELinux and AppArmor make it possible to confine processes beyond
    the traditional UNIX process and file permissions model. They
    restrict the QEMU process from accessing processes and files on the
    host system that are not needed by QEMU.<br>
    SELinux 와 AppArmor 는 전통적인 UNIX 프로세스 및 파일 권한 모델을 넘어 프로세스를 제한할 수 있게 합니다. QEMU 프로세스가 QEMU에서 필요하지 않은 호스트 시스템의 프로세스와 파일에 접근하는 것을 제한합니다.
-   Resource limits and cgroup controllers provide throughput and
    utilization limits on key resources such as CPU time, memory, and
    I/O bandwidth.<br>
    리소스 제한과 cgroup 컨트롤러는 CPU 시간, 메모리 및 I/O 대역폭과 같은 주요 리소스에 대한 처리량 및 활용도 제한을 제공합니다.
-   Linux namespaces can be used to make process, file system, and other
    system resources unavailable to QEMU. A namespaced QEMU process is
    restricted to only those resources that were granted to it.<br>
    리눅스 네임스페이스는 프로세스, 파일 시스템 및 기타 시스템 리소스를 QEMU에서 사용할 수 없도록 만들 수 있습니다. 네임스페이스가 지정된 QEMU 프로세스는 부여된 리소스에만 제한됩니다.
-   Linux seccomp is available via the QEMU `--sandbox` option. It
    disables system calls that are not needed by QEMU, thereby reducing
    the host kernel attack surface.<br>
    리눅스 seccomp 는 QEMU `--sandbox` 옵션을 통해 사용할 수 있습니다. QEMU에서 필요하지 않은 시스템 호출을 비활성화하여 호스트 커널 공격 표면을 줄입니다.

## Sensitive configurations<br>민감한 설정 사항

There are aspects of QEMU that can have security implications which
users & management applications must be aware of.<br>
QEMU에는 보안에 영향을 미칠 수 있기 때문에 사용자와 관리 응용 프로그램이 알아야 할 몇가지 사항이 있습니다.

### Monitor console (QMP and HMP)<br>모니터 콘솔 (QMP와 HMP)

The monitor console (whether used with QMP or HMP) provides an interface
to dynamically control many aspects of QEMU\'s runtime operation. Many
of the commands exposed will instruct QEMU to access content on the host
file system and/or trigger spawning of external processes.<br>
모니터 콘솔 (QMP 와 HMP 어느 쪽과 사용 되건) 은 QEMU의 런타임 동작의 많은 측면을 동적으로 제어하는 인터페이스를 제공합니다. 노출된 많은 명령은 QEMU가 호스트 파일 시스템의 내용에 접근하거나 외부 프로세스를 생성하도록 지시합니다.

For example, the `migrate` command allows for the spawning of arbitrary
processes for the purpose of tunnelling the migration data stream. The
`blockdev-add` command instructs QEMU to open arbitrary files, exposing
their content to the guest as a virtual disk.<br>
예를 들어, `migrate` 명령은 마이그레이션 데이터 스트림을 터널링(우회 전송)하기 위해 임의의 프로세스를 생성할 수 있습니다. `blockdev-add` 명령은 QEMU에게 임의의 파일을 열도록 지시하여 그 내용을 게스트에게 가상 디스크 형태로 노출시킵니다.

Unless QEMU is otherwise confined using technologies such as SELinux,
AppArmor, or Linux namespaces, the monitor console should be considered
to have privileges equivalent to those of the user account QEMU is
running under.<br>
QEMU가 달리 SELinux, AppArmor 또는 Linux 네임스페이스와 같은 기술을 사용하여 제한되지 않는 한, 모니터 콘솔은 QEMU가 실행되는 사용자 계정과 동등한 권한을 가진 것으로 간주되어야 합니다.

It is further important to consider the security of the character device
backend over which the monitor console is exposed. It needs to have
protection against malicious third parties which might try to make
unauthorized connections, or perform man-in-the-middle attacks. Many of
the character device backends do not satisfy this requirement and so
must not be used for the monitor console.<br>
모니터 콘솔이 노출되는 문자 장치 백엔드의 보안을 고려하는 것이 더욱 중요합니다. 불법적인 연결 또는 중간자 공격을 시도할 수 있는 악의적인 제3자로부터 보호되어야 합니다. 다수의 문자 장치 백엔드가 이 요구 사항을 충족하지 않으므로 모니터 콘솔에 사용해서는 안됩니다.

The general recommendation is that the monitor console should be exposed
over a UNIX domain socket backend to the local host only. Use of the TCP
based character device backend is inappropriate unless configured to use
both TLS encryption and authorization control policy on client
connections.<br>
일반적인 권고 사항은 모니터 콘솔은 UNIX 도메인 소켓 백엔드를 통해 로컬 호스트에만 노출되어야 합니다. TCP 기반 문자 장치 백엔드는 클라이언트 연결에 TLS 암호화 및 인증 제어 정책을 모두 사용하도록 구성되지 않는 한 적절하지 않습니다.

In summary, the monitor console is considered a privileged control
interface to QEMU and as such should only be made accessible to a
trusted management application or user.<br>
요약하면, 모니터 콘솔은 QEMU에 대한 하나의 특권 제어 인터페이스로 간주되므로 신뢰할 수 있는 관리 응용 프로그램 또는 사용자만 접근할 수 있도록 해야 합니다.
