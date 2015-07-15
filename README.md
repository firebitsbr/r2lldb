r2lldb
======

radare2-lldb integration

This repository contains all the necessary scripts required to debug
and manipulate anything running behind an LLDB the LLVM debugger from
an interactive radare2 session.

It has been developed for debugging iOS applications running on non
jailbroken devices. And it has been tested on arm32, arm64, i386
(simulator), and real OSX applications (x86-64).

The `debugserver` required to debug on jailbroken devices can be
obtained by using the `viatools/salvarez/ios-debugserver` repository.

In theory it shuold be possible to use r2lldb on Linux and debug
Android devices running gdbserver, but this hasn't been tested yet.


Features
--------

This is the list of features that r2lldb brings:

* Debug self-signed apps non-jailbroken devices with a low level debugger
* Trace calls to any address in memory
* Show backtrace
* List symbols
* List memory regions allocated
* List ObjC classes (using code injection)
* Inject dynamic libraries
* Step-in/over in r2's Visual mode
* Set breakpoints on objc methods
* Change process environment variables
* Run r2 commands on every traced breakpoint


Example
-------

Target:

	$ r2lldb -l 1234 /bin/ls

	or

	$ while : ; do debugserver *:1234 /bin/ls ; done
	Listening to port 1234 for a connection from *...
	Got a connection, launched process /bin/ls (pid = 82363).

Host: Terminal 1

	$ r2lldb -c :1234

	$ while : ; do lldb -s lldb/local ; done
	Listening for r2rap connections at port 9999

Host: Terminal 2

	$ r2lldb -r ios localhost:9999

	or

	$ r2 -i r2/ios rap://localhost:9999//

r2lldb's help from r2
---------------------

	[0x00000000]> =!?
	Usage: =![cmd] ...       # r2lldb integration
	=!?                      # show r2lldb's help (this one)
	=!help                   # show lldb's help
	=!i                      # target information
	=!is                     # list symbols
	=!dfv                    # show frame variables (arguments + locals)
	=!up,down,list           # lldb's command to list select frames and show source
	=!dks                    # stop debugged process
	=!dm                     # show maps (image list)
	=!dr                     # show registers
	=!dra                    # show all registers
	=!dr*                    # "" "" in r2 commands
	=!dr-*                   # remove all breakpoints
	=!db                     # list breakpoints
	=!db 0x12924             # set breakpoint at address
	=!db objc_msgSend        # set breakpoint on symbol
	=!dbo NSString init:     # set objc breakpoint
	=!dbt                    # show backtrace
	=!ds                     # step
	=!dcue                   # continue until entrypoint
	=!dso                    # step over
	=!dt                     # list all trace points
	=!dt 0x804040 =!dr       # add tracepoint for this address
	=!dc                     # continue
	=!dct                    # continue with tracing
	=!env                    # show process environment
	=!objc                   # list all objc classes
	=!setenv k v             # set variable in target process
	=!dlopen /path/to/lib    # dlopen lib (libr2.so, frida?)