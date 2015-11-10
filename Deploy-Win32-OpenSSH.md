1. Download [Zip file](https://github.com/PowerShell/Win32-OpenSSH/releases/download/11_09_2015/OpenSSH-Win32.zip)
* Extract contents
* Setup SSH host keys (**with no passphrase**)
     * `ssh-keygen.exe -t rsa -f ssh_host_rsa_key`
     * `ssh-keygen.exe -t dsa -f ssh_host_dsa_key`
     * `ssh-keygen.exe -t ecdsa -f ssh_host_ecdsa_key`
     * `ssh-keygen.exe -t ed25519 -f ssh_host_ed25519_key`
* Open Firewall
     * start PowerShell
     * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`
* If you need key-based authentication:
     * Install key-auth package
          * run setup-ssh-lsa.cmd
          * reboot
* Install and run daemon as NT Service running as Local System
     * `sshd.exe install`
     * `net start sshd`
     * To uninstall - `net stop sshd` ; `sshd.exe uninstall`
