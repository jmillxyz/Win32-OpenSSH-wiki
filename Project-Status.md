
Refer to project scope [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope)

#### High level tasks completed
 - POSIX compliant IO wrapper on Win32 Overlapped IO: this enables Unix based OpenSSH code to work for and on Windows. 
 - Visual Studio 2015 solution to build OpenSSH for Windows.
 - Secure architecture for Windows: OpenSSH server side architecture is altered for Windows adhering to its security principles. This is done with minimal impact to overall source layout. 
 - Secure ssh-agent: A different version of agent is authored for Windows to fit Windows security model and enable a Single Sign-On experience. 
 - VT100/ANSI TTY and PTY: Rewritten client side ANSI parser to accurately interpret and render VT100 stream on Windows console. Server side VT100 PTY that will support rich Windows console applications. Due to missing dependencies on Nano, this feature is only supported on full SKUs. 

#### Work in progress
 - Unicode support in Windows: Windows APIs are typically UTF-16 based while on Unix, its UTF-8. Details [here](https://github.com/PowerShell/Win32-OpenSSH/issues/319).  
 - POSIX File API support in Windows: Currently SFTP and SCP have significant Windows differentiated code. This enables effective Unix based code usage on Windows.  Tracked [here](https://github.com/PowerShell/Win32-OpenSSH/issues/320).

#### Work in pipeline
 - Enable PTY on nano server. 
 - Code cleanup and refactoring: In entire project specific logic has been ifdef'ed and spread out at various places in the project in an attempt to achieve a workable solution on Windows. These need to be cleaned up and refactored as per OpenBSD's coding guidelines in order to get to an acceptable state. Tracked [here] (https://github.com/PowerShell/Win32-OpenSSH/issues?q=is%3Aopen+is%3Aissue+label%3A%22Area-Code+Cleanup%22).
 - Syncing Win32 fork with the latest changes from OpenSSH main repo. Tracked [here] (https://github.com/PowerShell/Win32-OpenSSH/issues/328).
 - SAL annotate Win32 specific code. Run static analysis.Tracked [here] (https://github.com/PowerShell/Win32-OpenSSH/issues/329).
 - Fix bugs to be addressed for this [milestone](https://github.com/PowerShell/Win32-OpenSSH/milestone/1). 
 - Implement pester based basic E2E tests for Windows. Details [here] (https://github.com/PowerShell/Win32-OpenSSH/issues/330).

