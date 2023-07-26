# QEMU 문서 한글화

이 저장소는 QEMU의 문서를 한글화하기 위한 것입니다.<br>
QEMU 원래의 저장소는 https://www.qemu.org/contribute/ 참고 바랍니다.

## 번역 방법

### 번역할 파일 찾기

번역할 파일은 docs/ 디렉토리에 있습니다. 관심 있는 파일을 찾아서 번역 부탁드립니다.

### 번역하기

번역은 Markdown으로 작성합니다. Markdown에 대한 설명은 https://guides.github.com/features/mastering-markdown/ 참고 바랍니다.

### 번역한 파일 저장하기

번역한 파일은 docs/ 디렉토리에 .md 확장자로 저장합니다.

### 번역한 파일 커밋하기

번역한 파일을 커밋할 때는 커밋 메시지에 번역한 파일의 이름을 적어 주시면 어떨까요? 예를 들어 docs/system/device-emulation.md 파일을 번역했다면 커밋 메시지에 docs/system/device-emulation.md를 적습니다.

### 번역한 파일 푸시하기

번역한 파일을 푸시할 때는 이 저장소의 `doc-kr` 브랜치로 부터 파일 이름으로 분기하여 푸시해 주시면 어떨까요? 예를 들어 [`docs/system/device-emulation.md`](docs/system/device-emulation.md) 파일을 번역했다면 `doc-kr/system/device/emulation.md` 브랜치로 푸시합시다. 

### 번역한 파일 병합하기

번역하신 파일로 pull request 를 생성해 주시면 검토 후 `doc-kr/main`으로 병합할 계획입니다.

## 번역한 파일 목록

* docs/system/introduction.md
* docs/system/device-emulation.md

## 참고문헌

* https://www.qemu.org/docs/master/
* 최동욱 외, (실전 클라우드) 가상화 구축 기술 : QEMU KVM Xen에서 클라우드스택까지 가상화 구축과 관리의 모든 이야기, 한빛미디어, ISBN 9788979149616, 2012.
* Github Copilot
* Google Bard
