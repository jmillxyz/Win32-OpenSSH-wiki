## Build OpenSSH: (skip these steps if you’ve already done)
```powershell
Import-Module C:\git\openssh-portable\contrib\win32\openssh\OpenSSHBuildHelper.psm1 -Force
Build-OpenSSH -Configuration Debug -NativeHostArch x64
Install-OpenSSH -OpenSSHDir $env:SystemDrive\OpenSSH -Configuration Debug -NativeHostArch x64
```
## Run OpenSSH Pester Tests:
```powershell
Import-Module C:\git\openssh-portable\contrib\win32\openssh\OpenSSHTestHelper.psm1 -Force
Setup-OpenSSHTestEnvironment -Quiet -OpenSSHBinPath Setup-OpenSSHTestEnvironment -Quiet -OpenSSHBinPath
Run-OpenSSHE2ETest
```
   Note: If you want to run a particular test, just launch it by the script name:
```powershell
.\SCP.Tests.ps1
```

