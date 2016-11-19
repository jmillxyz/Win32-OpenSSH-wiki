**As of Nov 1st 2016, active development on Win32 fork is being done in https://github.com/PowerShell/openssh-portable.** This repo (https://github.com/PowerShell/Win32-OpenSSH) is being maintained to keep track of releases and issues. Win32-OpenSSH will be deprecated once changes in PowerShell/openssh-portable are integrated back into openssh/openssh-portable.



Refer to project scope [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope)

#### High level tasks completed
 - POSIX compliant IO wrapper on Win32 Overlapped IO: this enables Unix based OpenSSH code to work for and on Windows. 
 - Visual Studio 2015 solution to build OpenSSH for Windows.
 - Secure architecture for Windows: OpenSSH server side architecture is altered for Windows adhering to its security principles. This is done with minimal impact to overall source layout. 
 - Secure ssh-agent: A different version of agent is authored for Windows to fit Windows security model and enable a Single Sign-On experience. 
 - VT100/ANSI TTY and PTY: Rewritten client side ANSI parser to accurately interpret and render VT100 stream on Windows console. Server side VT100 PTY that will support rich Windows console applications. Due to missing dependencies on Nano, this feature is only supported on full SKUs. 
 - Unicode support in Windows: Windows APIs are typically UTF-16 based while on Unix, its UTF-8. All ssh binaries willon Windows can now take in Unicode arguments, support Unicode (UTF-8 or UTF-16) based configuration, key and log files, accurately encode file names going over SCP and SFTP.
 - POSIX File API support in Windows: SFTP and SCP code is cleaned up to effectively use original OpenSSH code on Windows.  
 - Syncing Win32 fork with the latest changes from OpenSSH main repo. All changes from OpenSSH 7.3 (latest stable source) are integrated in Win32 fork.

#### Work in progress
 - Code cleanup and refactoring: In entire project specific logic has been ifdef'ed and spread out at various places in the project in an attempt to achieve a workable solution on Windows. These need to be cleaned up and refactored as per OpenBSD's coding guidelines in order to get to an acceptable state. Tracked [here] (https://github.com/PowerShell/Win32-OpenSSH/issues?q=is%3Aopen+is%3Aissue+label%3A%22Area-Code+Cleanup%22).
 - Implement pester based basic E2E tests for Windows. Details [here] (https://github.com/PowerShell/Win32-OpenSSH/issues/330).
 - Enable an automated build and validation system.

#### Work in pipeline
 - SAL annotate Win32 specific code. Run static analysis. Tracked [here] (https://github.com/PowerShell/Win32-OpenSSH/issues/329).
 - Fix bugs to be addressed for this [milestone](https://github.com/PowerShell/Win32-OpenSSH/milestone/1). 


