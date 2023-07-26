
Keys in the character backend multiplexer<br>백엔드 문자 멀티플렉서 단축키[¶](#keys-in-the-character-backend-multiplexer "Permalink to this headline")
=====================================================================================================================


During emulation, if you are using a character backend multiplexer
(which is the default if you are using `-nographic`) then several
commands are available via an escape sequence. These key sequences all
start with an escape character, which is <kbd>Ctrl</kbd>+<kbd>a</kbd> by default, but can be
changed with `-echr`. The list below assumes you’re using the default.



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>h</kbd><br>Print this help



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>x</kbd><br>Exit emulator



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>s</kbd><br>Save disk data back to file (if `-snapshot`)



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>t</kbd><br>Toggle console timestamps



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>b</kbd><br>Send break (`magic sysrq` in Linux)



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>c</kbd><br>Rotate between the frontends connected to the multiplexer (usually
this switches between the monitor and the console)



<kbd>Ctrl</kbd>+<kbd>a</kbd> <kbd>Ctrl</kbd>+<kbd>a</kbd><br>Send the escape character to the frontend


