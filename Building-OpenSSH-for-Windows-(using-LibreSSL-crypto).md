#### Download latest source
  - git clone https://github.com/PowerShell/openssh-portable
  - git checkout latestw_all

#### Building OpenSSH for Windows
In Powershell:
  - cd repository-root
  - ipmo .\contrib\win32\openssh\OpenSSHBuildHelper.psm1 -Force
  - Start-OpenSSHBuild -Configuration <Release|Debug> -NativeHostArch <x64|x86>

#### Deploying OpenSSH for Windows
  - Start-OpenSSHPackage -Configuration <Release|Debug> -NativeHostArch <x64|x86>
  - Above generates Zipped binary and symbols payload. Follow further installation instructions [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH).

#### Building using Visual Studio 2015
  - If you are building **for the first time** then please "Building OpenSSH for Windows" above. It will download the required latest LibreSSL SDK.
  - Download and Install VS 2015 [Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)  
  - Open "contrib\win32\openssh\Win32-OpenSSH.sln" in Visual Studio and continue building Win32-OpenSSH binaries

