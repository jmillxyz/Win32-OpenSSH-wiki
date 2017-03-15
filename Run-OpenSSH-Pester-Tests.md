## Build OpenSSH: (skip these steps if you’ve already done)
```
Pushd .\openssh-portable\contrib\win32\openssh
Import-Module .\appveyor.psm1 –Force
Start-SSHBuild -Configuration Debug -NativeHostArch x64 –Verbose
Install-OpenSSH -OpenSSHDir $env:SystemDrive\OpenSSH -Configuration Debug -NativeHostArch x64
```
## Run OpenSSH Pester Tests:
- Deploy Open SSH tests
```
Install-TestDependencies
Deploy-OpenSSHTests -OpenSSHTestDir $env:SystemDrive\OpenSSH -Configuration Debug -NativeHostArch x64
cd $env:SystemDrive\OpenSSH
```
- Run pester tests : Launch powershell core at "C:\Program Files\PowerShell\6.0.0.12\powershell.exe" and run the below command:
```
Run-OpenSSHPesterTest –testRoot $env:SystemDrive\OpenSSH -outputXml testresult.xml
```
   Note: If you want to run a particular test, skip step 3 and just launch it:
  ```.\SCP.Tests.ps1 ```
