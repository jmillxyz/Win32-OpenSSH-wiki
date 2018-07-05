## Build OpenSSH: (skip these steps if you’ve already done)
```powershell
Import-Module C:\git\openssh-portable\contrib\win32\openssh\OpenSSHBuildHelper.psm1 -Force
Start-OpenSSHBuild -Configuration Debug -NativeHostArch x64
```

## Run OpenSSH E2E Tests:
```powershell
Import-Module C:\git\openssh-portable\contrib\win32\openssh\OpenSSHTestHelper.psm1 -Force
Set-OpenSSHTestEnvironment -Confirm:$false -OpenSSHBinPath c:\openSSH
Invoke-OpenSSHE2ETest
```
   Note: If you want to run a particular test, just launch it by the script name:
```powershell
#need to explicitly import pester module on win7 only
import-module pester
.\SCP.Tests.ps1
```

