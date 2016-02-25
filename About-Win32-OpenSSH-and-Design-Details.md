Intro
-----
[OpenSSH](https://en.wikipedia.org/wiki/OpenSSH), part of OpenBSD operating system, is a bunch of utility programs based on [SSH](https://en.wikipedia.org/wiki/Secure_Shell) protocol. These include server and client executables as well as utilities to create and manage cryptographic keys. [Portable OpenSSH](http://www.openssh.com/portable.html) is derived from the OpenBSD project and ported to support a wide variety of Unix flavors. The goal of this project is to extend support to Windows family of operating systems and be able to do this in a common repository without needing a Windows specific fork. 

Relevant design details in the context of this project -     
OpenSSH and the portable version are single threaded applications, interacting with IO using POSIX based [File Descriptors](https://en.wikipedia.org/wiki/File_descriptor) and multiplexing IO operations using [select] (https://en.wikipedia.org/wiki/Select_%28Unix%29) calls. Session isolation and [privilege separation](http://www.citi.umich.edu/u/provos/ssh/privsep.html) are implemented using the standard UNIX routines - [fork](https://en.wikipedia.org/wiki/Fork_(system_call)), [setuid](https://en.wikipedia.org/wiki/Setuid), [chroot](https://en.wikipedia.org/wiki/Chroot). IPC is carried over [UNIX domain sockets](https://en.wikipedia.org/wiki/Unix_domain_socket). 


Goals
-----
As stated earlier, the main goal is side by side Windows support in the portable version of OpenSSH. The project is currently being worked on a fork of OpenSSH7.1p1 - here after, called win32-fork code and main code respectively. The plan is get this fork to a state that could integrate into the latest version in main, sometime around May-June this year, with minimum impact to main sources. Obviously, we would want to reuse the main code as much as possible, whilst respecting the fundamental differences between Unix and Windows operating systems. 

Guidelines
-----------
To prevent any regressions in main and to enable easier review of the changes coming from win32-fork, there will be no "main" code moving or refactoring in win32-fork. There are multiple places where platform abstraction makes sense (auth, console to name a few), but this wont be addressed in the fork as it would lead to significant code churn. This will be done post integration once we have stable Windows supported version with significant test coverage living in main repo. Crypto support using Windows [CNG](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376210(v=vs.85).aspx) has been tested out in win32-fork but since it needed reasonable modifications to original code,  relevant changes will be reverted\undone. This means that the Windows supported version potentially available mid this year will rely on OpenSSL's crypto (exception is SSP for key-based authentication that will use CNG - more details later). Coding style will follow [OpenBSD guidelines](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man9/style.9?query=style&sec=9).

Design details
-------------
Here, we'll discuss the design choices aimed at keeping majority of code base common between Unix and Windows, deviating where deemed necessary.

### POSIX IO
POSIX IO calls are a significant part of OpenSSH code. A POSIX IO wrapper will be implemented on top of Win32 async File IO. This wrapper strictly implements the POSIX IO needs of OpenSSH keeping the code differences, especially in the transport layer, to a minimum. Note that the wrapper implements only the calls needed by OpenSSH (and not all defined in POSIX standard). Specifically, the wrapper implements
+ IO calls creating file descriptors - open, creat, socket, accept, socketpair, pipe
+ operations on a single file descriptor - fd_set, FD_* macros, fcntl, read, write, recv, send, fstat, fdopen, close, dup and dup2
+ operations on multiple file descriptors - select
+ signal semantics on these operations - ex. select (or any blocking IO call) returning EINTR
+ Apart from these, the wrapper also bridges the gap on POSIX signal(). Details below.

Design summary of POSIX wrapper
+ Single threaded (not thread safe based on current needs but can be made so if needed going forward). 
+ Maintains file descriptor table and its state. Table stores mapping between file descriptors (int) and associated Windows IO state (handle, buffers, async call state, etc). 
+ fd_set implemented as a bit array, lowest available file descriptors get allocated first.
+ Calls underlying Win32 APIs that provide [Overlapped](https://en.wikipedia.org/wiki/Overlapped_I/O) semantics. 
+ "select" and blocking IO calls go through a common "wait_for_any" function that wakes up on
  - IO completions
  - Signals
  - Timers
+ All underlying Win32 IO API calls are made asynchronous (non-blocking). Blocking semantics are implemented within the wrapper by an explicit "wait_for_any" for IO to complete.
+ FD_CLOEXEC is supported, setting this flag denies inheritance of underlying Windows handle. 
+ Uses [APCs](https://msdn.microsoft.com/en-us/library/windows/desktop/ms681951(v=vs.85).aspx) wherever available and minimzing use of [events](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682655(v=vs.85).aspx). This simplifies code and has performance benefits.
+ Maintains internal buffers to accommodate a fundamental underlying difference between POSIX and Win32 IO async models - IOReady Vs IOComplete (Ex for a Read operation, POSIX APIs signal when IO is ready - data will be subsequently explicitly read, Win32 APIs signal when IO has completed - data is already copied to a user provided buffer. Though the internal buffer and additional copy may seem to be a performance hit, a validation exercise did not show any major impact. It in fact proved beneficial in reducing kernel calls during "read"s (ex. reading a header, would fetch the entire packet in a single call). 
+ Maintains Interrupt queue and interrupt handler mapping table. Queue is maintained to temporarily hold interrupts that are otherwise supported in Windows but handled in a different approach typically in a different thread. Ex SIGINT, SIGWINCH. These are processed inside of "wait_for_any" in the main thread. On processing any queued interrupt, this function will return SIGINT.
+ Details on different interrupts handled by OPENSSH code and how they will be handled in Windows

| Signal | Detail |
|:-------|:-------|
|SIGINT  |Windows invokes its Ctrl+C handler on a different thread, that handler queues the interrupt in the internal queue and handled by any of the blocking calls (select, etc) |
|SIGWINCH|Like Ctrl+C, a console windows size change event is captured in a native handled, queued and processed in one of the blocking calls |
| SIGILL, SIGTERM, SIGQUIT, WJSIGNAL,SIGTTIN, SIGTTOU | Not generated in Windows, these may be defined by handlers will never be invoked |
| SIGHUP | TBD |
| SIGCHLD | TBD |
|SIGTSTP | TBD |
|SIGPIPE | only accepts SIG_IGN handler (that's all OpenSSH uses) |
|SIGALARM | implemented internally inside the wrapper. handler automatically called when native timer set by CreateWaitableTimer expires |

+ Additional details on underlying Win32 calls used

| POSIX IO call  |  Underlying Win32 IO call(s) | Additional details |
|:-----|:-----|:-----|
| accept | AcceptEx | No APC semantics so, explicit event used for async IO   |
| connect | ConnectEx | No APC semantics so, explicit event used for async IO |
| send | WSASend |  |
| recv | WSARecv |   |
| open | CreateFile |   |
| creat | CreateFile |  |
| pipe | CreateNamedPipe  | A uni directional named pipe with and internal name is created, CreateFile called to connect from other end  |
| read | ReadFileEx |   |
| write | WriteFileEx |   |
| fdopen | TBD |    |
| fstat |  TBD |     |
| dup, dup2 | SetStdHandle | only supported on standard IO file descriptors (used for IO redirection) |
| socketpair | CreateNamedPipe | A bi directional named pipe with an internal name is created, CreateFile called to connect from other end. This does not support AF_UNIX ancilliary messages. More details later |
| setitimer | CreateWaitableTimer |

A fully functional prototype (for socket, file and pipe IO) of this wrapper is available [here](https://github.com/PowerShell/Win32-OpenSSH/tree/L2-Win32Posix-Prototype/contrib/win32/w32-posix-prototype/win32posix.)

#### fork()
There is no easy fork() equivalent in Windows. fork() is used in OpenSSH in multiple places, of those - 3 are worth mentioning 
+ Session isolation: Each accepted connection in sshd is handed off and processed in a forked child. This will be implemented in Windows using CreateProcess based custom logic - will need #def differentiated code between Unix and Windows
+ Privilege separation: Implemented in OpenSSH by processing and parsing network data in forked and underprivileged child processes that communicate to privileged Monitor process through IPC. Monitor does the core crypto validation and authentication. Privilege downgrading is done by setuid(restricted_user). Security model in Windows will be different, running the SSHD service itself in a low privileged mode. So, the whole Privilege separation relevant code is not needed and will be disabled for Windows. 
+ sftp and scp: sftp and scp client side utilities invoke ssh using fork() and exec(). This logic will be disabled and substituted with CreateProcess based one.
+ The rest of the places fork() is used is listed below. None of these are critical to the functionality in Windows and will be appropriately disabled for Windows.
  - TBD

#### AF_UNIX domain sockets
Unix domain sockets are used for IPC communication between processes on the same host. Apart from providing stream/datagram modes, they also support a secure way to transmit ancillary data (like file descriptors). The only place ancillary data is used in OpenSSH is in "ProxyUseFDPass" feature where a proxy command is issued by ssh client to create a connected socket, and its FD is transmitted back over IPC. This feature will be disabled on Windows. The rest of the places AF_UNIX sockets are used:
+ ControlMaster - used to multiplex multiple sessions over a single SSH connection. 
+ SSHAgent - used to managed store keys and crypto validation based on those. Current plan is to replace its client side usage with Windows Credential manager. We will discuss its relevance on server side later in this page.
+ Local Socket Forwarding - This is forwarding traffic to AF_UNIX sockets and this feature is not applicable in Windows
+ SSHD rexec - Not applicable for Windows. SSHD will be implemented as Windows service, that can be configured for auto restart.
+ SSHD from inetd - Not applicable for Windows. 

AF_UNIX channel will be implemented using secure bidirectional named pipes in Windows. This does not support ancillary data but is sufficient for above listed features relevant in Windows. 

#### Privilege Separation and Security model in Windows (tentative design)
SSHD will be implemented as a Windows service, running in its [virtual account](https://technet.microsoft.com/en-us/library/dd548356.aspx) context - NT Service\SSHD - this is a restricted account that will only be granted the following needed privileges - to be added. 

SSHD host public keys and configuration files will be [ACL](https://msdn.microsoft.com/en-us/library/windows/desktop/aa374872(v=vs.85).aspx)ed to allow READ by NT Service\SSHD. SSHD host private keys are ACLed to admin-only access - mandating that relying signature generation happens in a privileged process. We will be leveraging ssh-agent for this purpose, adding additional logic to launch it automatically on demand  (most likely using COM). ssh-agent will serve all signature requests irrespective of whether the private key is password protected/not.

As detailed earlier, session isolation in Windows will be done using CreateProcess based custom logic (in place of fork based logic in Unix). Spawned child process will run as NT Service\SSHD too.

Authentication logic will be different between Windows and Unix. Password authentication is done using [LogonUser](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378184(v=vs.85).aspx) while 
Key-based authentication will be implemented in a custom [SSP](https://msdn.microsoft.com/en-us/library/windows/desktop/aa380497(v=vs.85).aspx) that is executed in [lsass](https://msdn.microsoft.com/en-us/library/aa939478(v=winembedded.5).aspx) (a privileged process). 

End result of authentication in Windows is a Windows user token (if authentication succeeds). SSH sessions that need client user capabilities are hosted in processes running under the context of client user (launched using CreateProcess(user_token)). Ex. cmd.exe for terminal session, sftp_server.exe for sftp session and scp.exe for scp session.





 






 






