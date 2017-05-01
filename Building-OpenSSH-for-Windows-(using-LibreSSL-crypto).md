#### Download latest source
  - git clone https://github.com/PowerShell/openssh-portable
  - git checkout latestw_all_libressl

#### Building OpenSSH for Windows
In Powershell:
  - cd repository-root
  - ipmo .\contrib\win32\openssh\OpenSSHBuildHelper.psm1 -Force
  - Build-OpenSSH -Configuration <Release|Debug> -NativeHostArch <x64|x86>

#### Deploying OpenSSH for Windows
  - Copy libreSSL crypto dll to a known PATH
    - copy .\contrib\win32\openssh\LibreSSLSDK\x64\libcrypto-41.dll (Join-Path $env:windir system32)
  - Package-OpenSSH -Configuration <Release|Debug> -NativeHostArch <x64|x86>
  - Above generates Zipped binary and symbols payload. Follow further installation instructions [here].

#### Building using Visual Studio 2015
  - Download and Install VS 2015 [Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)  
  - Open "contrib\win32\openssh\Win32-OpenSSH.sln" in Visual Studio and continue building Win32-OpenSSH binaries

