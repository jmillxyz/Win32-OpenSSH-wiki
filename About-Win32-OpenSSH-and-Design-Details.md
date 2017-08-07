Intro
-----
[OpenSSH](https://en.wikipedia.org/wiki/OpenSSH), part of OpenBSD operating system, is a bunch of utility programs based on [SSH](https://en.wikipedia.org/wiki/Secure_Shell) protocol. These include server and client executables as well as utilities to create and manage cryptographic keys. [Portable OpenSSH](http://www.openssh.com/portable.html) is derived from the OpenBSD project and ported to support a wide variety of Unix flavors. The goal of this project is to extend support to Windows family of operating systems and be able to do this in a common repository without needing a Windows specific fork. 

Relevant design details in the context of this project -     
OpenSSH and the portable version are single threaded applications, interacting with IO using POSIX based [File Descriptors](https://en.wikipedia.org/wiki/File_descriptor) and multiplexing IO operations using [select](https://en.wikipedia.org/wiki/Select_%28Unix%29) calls. Session isolation and [privilege separation](http://www.citi.umich.edu/u/provos/ssh/privsep.html) are implemented using the standard UNIX routines - [fork](https://en.wikipedia.org/wiki/Fork_(system_call)), [setuid](https://en.wikipedia.org/wiki/Setuid), [chroot](https://en.wikipedia.org/wiki/Chroot). IPC is carried over [UNIX domain sockets](https://en.wikipedia.org/wiki/Unix_domain_socket). 


Goals
-----
As stated earlier, the main goal is side by side Windows support in the portable version of OpenSSH. The project is currently being worked on a fork of OpenSSH7.1p1 - here after, called windows-fork code and main code respectively. The plan is get this fork to a state that could integrate into the latest version in main, with minimum impact to main sources. Obviously, we would want to reuse the main code as much as possible, whilst respecting the fundamental differences between Unix and Windows operating systems. 

Guidelines
-----------
To prevent any regressions in main and to enable easier review of the changes coming from win32-fork, there will be no "main" code moving or refactoring in windows-fork. There are multiple places where platform abstraction makes sense (auth, console to name a few), but this wont be addressed in the fork as it would lead to significant code churn. This will be done post integration once we have stable Windows supported version with significant test coverage living in main repo. Coding style will follow [OpenBSD guidelines](http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man9/style.9?query=style&sec=9).

Design details
-------------
Here, we'll discuss the design choices aimed at keeping majority of code base common between Unix and Windows, deviating where deemed necessary.

### POSIX IO
POSIX IO calls are a significant part of OpenSSH code. A POSIX IO wrapper will be implemented on top of Win32 async File IO. This wrapper strictly implements the POSIX IO needs of OpenSSH keeping the code differences, especially in the transport layer, to a minimum. Note that the wrapper implements only the calls needed by OpenSSH (and not all defined in POSIX standard). Specifically, the wrapper implements
+ IO calls creating file descriptors - open, creat, socket, accept, socketpair, pipe
+ operations on a single file descriptor - fd_set, FD_* macros, fcntl, read, write, recv, send, fstat, fdopen, close, dup and dup2
+ operations on multiple file descriptors - select
+ signal semantics on these operations - ex. select (or any blocking IO call) returning EINTR
+ Apart from these, the wrapper also bridges the gap in using POSIX signals. Details below.

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
|SIGINT  |Windows invokes its Ctrl+C handler on a different thread, that handler queues the interrupt in the internal queue and schedules an APC on main thread |
|SIGWINCH|Like Ctrl+C, a console windows size change event is captured in a native handled, queued and processed in one of the blocking calls |
| SIGILL, SIGTERM, SIGQUIT, WJSIGNAL,SIGTTIN, SIGTTOU, SIGHUP | Not generated in Windows, these may be defined by handlers will never be invoked |
| SIGCHLD | Implemented by listening (WaitForAnyObject) on child process handles |
|SIGTSTP | See SIGINT |
|SIGPIPE | Implemented by scheduling an APC call back |
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
| dup, dup2 | SetStdHandle | only supported on standard IO file descriptors (used for IO redirection) |
| setitimer | CreateWaitableTimer |

#### fork()
There is no easy fork() equivalent in Windows. fork() is used in OpenSSH in multiple places, of those - 3 are worth mentioning 
+ Session isolation: Each accepted connection in sshd is handed off and processed in a forked child. This will be implemented in Windows using CreateProcess based custom logic - will need #def differentiated code between Unix and Windows
+ Privilege separation: Implemented in OpenSSH by processing and parsing network data in forked and underprivileged child processes that communicate to privileged Monitor process through IPC. Monitor does the core crypto validation and authentication. Privilege downgrading is done by setuid(restricted_user). 
While privilege separation is ideal, it requires adding in complexity and refactoring to accommodate a Windows specific solution along with a Unix based one in a common architecture. 
The plan is to have a initial Windows version with no privilege separation. In Windows, ssh daemon will run under the context of its service account - NT Service\SSHD.
+ sftp and scp: sftp and scp client side utilities invoke ssh using fork() and exec(). This logic will be substituted with CreateProcess based one.

#### AF_UNIX domain sockets
Unix domain sockets are used for IPC communication between processes on the same host. Apart from providing stream/datagram modes, they also support a secure way to transmit ancillary data (like file descriptors). The only place ancillary data is used in OpenSSH is in "ProxyUseFDPass" feature where a proxy command is issued by ssh client to create a connected socket, and its FD is transmitted back over IPC. This feature will be disabled on Windows. The rest of the places AF_UNIX sockets are used:
+ ControlMaster - used to multiplex multiple sessions over a single SSH connection. This feature passes on ancillary data (file descriptors) over the socket connection.
+ SSHAgent - used to managed store keys and crypto validation based on those. SSH agent and key management for Windows are discussed later in this document.
+ Local Socket Forwarding - This is forwarding traffic to AF_UNIX sockets.
+ SSHD rexec - Not applicable for Windows. SSHD will be implemented as Windows service, that can be configured for auto restart.
+ SSHD from inetd - Not applicable for Windows. 

AF_UNIX channel will be implemented using secure bidirectional named pipes in Windows. Support for ancillary data will be added in a limited form to support ControlMaster. 

#### Security model in Windows
SSHD will be implemented as a Windows service. Unlike in Unix (sshd runs as root), it runs in a less privileged [service account](http://sourcedaddy.com/windows-7/understanding-service-sids.html) - "NT Service\SSHD". This service account is assigned only one needed privilege - [SeAssignPrimaryTokenPrivilege](https://msdn.microsoft.com/en-us/library/windows/desktop/bb530716(v=vs.85).aspx). This privilege is needed to launch session processes on client's behalf.

ssh-agent will be reimplemented for Windows as a Windows service, running as LocalSystem with TCB privileges (equivalent to root on Linux). Unlike in Unix, ssh-agent will listen on a known static IPC port. This is done as a security measure to protect ssh-agent port from hijack/spoof attacks. It serves the following "key-agent" requests that need be processed at SYSTEM privilege level:
+ Register a host key - All host keys, to be used by ssh deamon for host authentication can be securely registered with ssh-agent.  The registration process will be similar to ssh-add usage in Unix. Host keys will be internally encrypted using DPAPI using OS System account.
+ Register a user key - User keys, can be securely one-time registered with ssh-agent for a single sign-on experience. These keys are DPAI encrypted using user's password and ACL'ed as SYSTEM only. This ensures that malware running under user's context can never steal key material.
+ Delete a host or a user key - Similar to ssh-add usage in Unix. 
+ signature generation and validation - using a registered key. 

The above listed requests are similar to what ssh-agent serves in Unix. Since sshd runs as a low privileged service, we need an equivalent of Unix's privileged monitor that can serve any privileged operations. Ssh-agent on Windows also hosts a privileged monitor/broker that serves the following operations:
+ Key-Based authentication. It will be responsible for generating the client/user token once authentication succeeds. This includes - ensuring validity of public key mapping, validating a signed payload as part of client key based authentication and generating a Windows user token. Token generation is done using S4U for domain accounts and a custom SSP for local accounts. 
+ User profile management - user profiles are loaded upon sshd's request. User profile typically needs to be loaded for interactive sessions. 

As detailed earlier, session isolation in Windows will be done using CreateProcess based custom logic (in place of fork based logic in Unix). Spawned child process will run as NT Service\SSHD too.

End result of authentication in Windows is a Windows user token (if authentication succeeds). SSH sessions that need client user capabilities are hosted in processes running under the context of client user (launched using CreateProcess(user_token)). Ex. cmd.exe for terminal session, sftp_server.exe for sftp session and scp.exe for scp session.
This means that in order to login via ssh, one needs to have a valid Windows account on the target.
If user profile does not exist, it gets created upon first logon via ssh. This would typically (in default sshd configuration) be via password based authentication (since profile does not exist for authorized_keys to be placed).

Shown below is a high level overview of the various SSH components and access boundaries for various resources involved: 

![HLA](https://cloud.githubusercontent.com/assets/14185020/26595943/de433644-4521-11e7-9a48-78bf7d3dbb17.JPG)


 






 






