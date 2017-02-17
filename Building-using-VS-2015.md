#### Building using Visual Studio 2015

Download and Install VS 2015 [Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)  

Clone https://github.com/PowerShell/openssh-portable

OpenSSL SDK payload is hosted at https://github.com/PowerShell/Win32-OpenSSH/tree/L1-Prod/contrib/win32/openssh/OpenSSLSDK. Copy OpenSSLSDK and place it under contrib/win32/openssh

Open "contrib\win32\openssh\Win32-OpenSSH.sln" in Visual Studio and continue building Win32-OpenSSH binaries

If you see crt headers related errors on recompilations, try deleting the following file, and do a clean build subsequently

contrib\win32\win32compat\inc\crtheaders.h
