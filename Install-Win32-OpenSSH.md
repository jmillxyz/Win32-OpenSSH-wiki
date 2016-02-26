## Install Win32 OpenSSH (test release)

1. Download the latest [64-bit](https://github.com/PowerShell/Win32-OpenSSH/releases/download/latest/OpenSSH-Win64.zip) or [32-bit](https://github.com/PowerShell/Win32-OpenSSH/releases/download/latest/OpenSSH-Win32.zip) release
* Extract contents to `C:\Program Files\OpenSSH-Win32`
* Start Powershell as Administrator
     * `cd 'C:\Program Files\OpenSSH-Win32'`
* Setup SSH host keys (this will generate all the 'host' keys that sshd expects when its starts)
     * `.\ssh-keygen.exe -A`
* Open Firewall
     * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`
* If you need key-based authentication, run the following to setup the key-auth package
          
    * `powershell.exe .\install-sshlsa.ps1`
    * `Restart-Computer`

* Install and run daemon as NT Service running as Local System
     * `.\sshd.exe install`
     * `Start-Service sshd`
     * Make the service start on boot (PowerShell): `Set-Service sshd -StartupType Automatic`

`New-NetFirewallRule` is for servers only. If you're on a workstation try:

```
netsh advfirewall firewall add rule name='SSH Port' dir=in action=allow protocol=TCP localport=22
```

## Uninstall Win32 OpenSSH

* Start Powershell as Administrator
* Stop the service
     * `Stop-Service sshd`
* Uninstall
     * `.\sshd.exe uninstall`
     * `powershell .\uninstall-sshlsa.ps1`
     * Reboot if you need to install a newer version of Win32-OpenSSH
