
Refer to project scope [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope)

#### High level tasks completed
 - POSIX compliant IO wrapper on Win32 Overlapped IO: this enables Unix based OpenSSH code to work for and on Windows. 
 - Visual Studio 2015 solution to build OpenSSH for Windows.
 - Secure architecture for Windows: OpenSSH server side architecture is altered for Windows adhering to its security principles. This is done with minimal impact to overall source layout. 
 - Secure ssh-agent: A different version of agent is authored for Windows to fit Windows security model and enable a Single Sign-On experience. 

#### Work in progress (ETA - 9/23)
 - Improving Terminal experience: Interactive remote shell experience in the current version of this project is impacted by a bunch of [issues](https://github.com/PowerShell/Win32-OpenSSH/issues?q=is%3Aopen+is%3Aissue+label%3A%22Terminal+experience%22). Following is being done to address these:
    - Rewriting client side ANSI parser to enable ssh client to accurately interpret and render VT100 (mapping VT100 to Windows console calls). 
    - Enable ssh server to support rich Windows console application over remote sessions (mapping Windows console calls to VT100)
    - Validating the following key strokes behavior on multiple SSH targets - CR+LF, BS, Tab and Arrow Keys. 

#### Work in pipeline
 - Unicode support in Windows: Windows APIs are typically UTF-16 based while on Unix, its UTF-8. Need to figure out a workable solution that would support both models.  
 - POSIX File API support in Windows: Currently SFTP and SCP have significant Windows differentiated code. This enables effective Unix based code usage on Windows.  
 - Code cleanup and refactoring: In entire project specific logic has been ifdef'ed and spread out at various places in the project in an attempt to achieve a workable solution on Windows. These need to be cleaned up and refactored as per OpenBSD's coding guidelines in order to get to an acceptable state. 
 - Syncing Win32 fork with the latest changes from OpenSSH main repo.
 - SAL annotate Win32 specific code. Run static analysis.
 - Fix bugs to be addressed for this [milestone](https://github.com/PowerShell/Win32-OpenSSH/milestone/1). 

