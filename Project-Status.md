**As of Nov 1st 2016, active development on Win32 fork is being done in https://github.com/PowerShell/openssh-portable.**

This repo (https://github.com/PowerShell/Win32-OpenSSH) is being maintained to keep track of releases and issues. Win32-OpenSSH will be deprecated once changes in PowerShell/openssh-portable are integrated back into openssh/openssh-portable.

Win32-OpenSSH is shipped as FOD (Feature On Demand) in Windows 10 fall creator update. For more information, please visit our [official blog](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/).

Refer to project scope [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope)

#### Work in progress
 - Following up on minor issues identified via penetration testing. 
 - Conversation with upstream community - seeking their feedback and evaluating integration plans. PR posted [here](https://github.com/openssh/openssh-portable/pull/63). Contentious areas of logic identified and refactoring work to converge Unix and Windows code is in progress.

#### High level tasks completed
 - POSIX compliant IO wrapper on Win32 Overlapped IO: this enables Unix based OpenSSH code to work for and on Windows. 
 - Visual Studio 2015 solution to build OpenSSH for Windows.
 - Secure architecture for Windows: OpenSSH server side architecture is altered for Windows adhering to its security principles. This is done with minimal impact to overall source layout. 
 - Secure ssh-agent: A different version of agent is authored for Windows to fit Windows security model and enable a Single Sign-On experience. 
 - VT100/ANSI TTY and PTY: Rewritten client side ANSI parser to accurately interpret and render VT100 stream on Windows console. Server side VT100 PTY that will support rich Windows console applications. Due to missing dependencies on Nano, this feature is only supported on full SKUs. 
 - Unicode support in Windows: Windows APIs are typically UTF-16 based while on Unix, its UTF-8. All ssh binaries on Windows can now take in Unicode arguments, support Unicode (UTF-8) based configuration, key and log files, accurately encode file names going over SCP and SFTP.
 - POSIX File API support in Windows: SFTP and SCP code is cleaned up to effectively use original OpenSSH code on Windows.  
 - Syncing Win32 fork with the latest changes from OpenSSH main repo. Windows fork is being maintained up-to-date with upstream repo.
 - Code cleanup and refactoring - Common OpenSSH code is cleaned up and ready to take upstream. Changes in [this](https://github.com/PowerShell/openssh-portable/tree/latestw) branch are ready for community feedback. 
 - Automated build and validation system - automated Windows builds, unittest and E2E test runs is now enabled.
 - Evaluated Microsoft CNG support in OpenSSH. Since this requires significant crypto interface refactoring, its decided to switch to LibreSSL (in place of OpenSSL). 
 - Improved overall test coverage on Windows. Pester based E2E coverage is still pretty minimal compared to bash scripts based coverage on Unix. Plan is to progressively add more coverage going forward. Any contributions here are very welcome. 
 - Initial [Penetration Testing OpenSSH](https://blogs.msdn.microsoft.com/powershell/2017/05/01/openssh-security-testing-kick-off/) done. 





