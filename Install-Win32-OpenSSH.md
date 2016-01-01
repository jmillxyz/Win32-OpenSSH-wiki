## Install Win32 OpenSSH

1. Download [the 2015-11-09 release](https://github.com/PowerShell/Win32-OpenSSH/releases/download/11_09_2015/OpenSSH-Win32.zip)
* Extract contents to `C:\Program Files\OpenSSH-Win32`
* Start Powershell as Administrator
     * `cd 'C:\Program Files\OpenSSH-Win32'`
* Setup SSH host keys (this will generate all the 'host' keys that sshd expects when its starts)
     * `.\ssh-keygen.exe -A`
* Open Firewall
     * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`
* If you need key-based authentication:
     * Install key-auth package
          * run setup-ssh-lsa.cmd
          * reboot
* Install and run daemon as NT Service running as Local System
     * `.\sshd.exe install`
     * `net start sshd`
     * Make the service start on boot (PowerShell): `Set-Service sshd -StartupType Automatic`

## Uninstall Win32 OpenSSH

* Start Powershell as Administrator
* Stop the service
     * `net stop sshd`
* Uninstall
     * `.\sshd.exe uninstall`
