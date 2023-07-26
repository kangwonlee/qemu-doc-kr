
Keys in the character backend multiplexer<br>백엔드 문자 멀티플렉서 단축키[¶](#keys-in-the-character-backend-multiplexer "Permalink to this headline")
=====================================================================================================================


During emulation, if you are using a character backend multiplexer
(which is the default if you are using `-nographic`) then several
commands are available via an escape sequence. These key sequences all
start with an escape character, which is <kbd>Ctrl</kbd>+<kbd>a</kbd> by default, but can be
changed with `-echr`. The list below assumes you’re using the default.<br>
에뮬레이션 중, 문자 백엔드 멀티플렉서를 사용한다면 (만약 `-nographic`를 사용한다면 기본값입니다) 이스케이프 시퀀스를 통해 몇몇 명령어를 사용할 수 있습니다. 이 키 시퀀스들은 모두 이스케이프 문자로 시작합니다. 기본값은 <kbd>Ctrl</kbd>+<kbd>a</kbd>이지만 `-echr`로 변경할 수 있습니다. 아래 목록은 기본값을 사용한다고 가정합니다.



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>h</kbd><br>Print this help<br>이 도움말을 출력



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>x</kbd><br>Exit emulator<br>에뮬레이터를 나감



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>s</kbd><br>Save disk data back to file (if `-snapshot`)<br>디스크 데이터를 파일로 저장



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>t</kbd><br>Toggle console timestamps<br>콘솔 타임스탬프 표시 여부 전환



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>b</kbd><br>Send break (`magic sysrq` in Linux)<br>브레이크 신호 전송 (리눅스 `magic sysrq`)



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>c</kbd><br>Rotate between the frontends connected to the multiplexer (usually
this switches between the monitor and the console)<br>
여러 프론트엔드 사이를 전환 (보통 모니터와 콘솔 사이를 전환)


<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>Ctrl</kbd>+<kbd>a</kbd><br>Send the escape character to the frontend<br>
프론트엔드로 이스케이프 문자 전송
