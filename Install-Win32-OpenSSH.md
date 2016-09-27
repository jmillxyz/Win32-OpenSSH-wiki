## Install Win32 OpenSSH (test release)

1. Download the [latest](https://github.com/PowerShell/Win32-OpenSSH/releases/latest/) build. To get links to latest downloads - see [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/How-to-retrieve-links-to-latest-packages). A [Chocolatey package](https://chocolatey.org/packages/win32-openssh) is also available if you prefer. For compat issues on Nano see [issues on Nano](https://github.com/PowerShell/Win32-OpenSSH/issues/234)
* Extract contents to `C:\Program Files\OpenSSH`
* Start Powershell as Administrator
     * `cd 'C:\Program Files\OpenSSH'`
* Install sshd and ssh-agent services. 
     * `powershell -executionpolicy bypass -file install-sshd.ps1`
* Setup SSH host keys (this will generate all the 'host' keys that sshd expects when its starts)
     * `.\ssh-keygen.exe -A`
* Secure SSH host keys (optional)
     * `Start-Service ssh-agent`
     * download psexec from [here](https://technet.microsoft.com/en-us/sysinternals/pxexec.aspx)
     * launch cmd.exe as SYSTEM - `psexec.exe -i -s cmd.exe`
     * register host keys in above cmd.exe
     * `ssh-add ssh_host_dsa_key`
     * `ssh-add ssh_host_rsa_key`
     * `ssh-add ssh_host_ecdsa_key`
     * `ssh-add ssh_host_ed25519_key`
     * host private keys are now securely stored by ssh-agent, private key files can be removed at this point.
* Open Firewall
     * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`
* If you need key-based authentication, run the following to setup the key-auth package
          
    * `powershell -executionpolicy bypass -file install-sshlsa.ps1` (for Win7 and Server 2008, see [here](https://github.com/PowerShell/Win32-OpenSSH/issues/189))
    * `Restart-Computer`
* Set sshd in auto-start mode and open up firewall (optional)
     * `Set-Service sshd -StartupType Automatic`
     * `Set-Service ssh-agent -StartupType Automatic`
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
     * `powershell.exe .\uninstall-sshd.ps1`
     * `powershell .\uninstall-sshlsa.ps1` (for Win7 and Server 2008, see [here](https://github.com/PowerShell/Win32-OpenSSH/issues/189))
     * Reboot if you need to install a newer version of Win32-OpenSSH
