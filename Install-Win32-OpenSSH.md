## Install Win32 OpenSSH (test release)

* Download the [latest](https://github.com/PowerShell/Win32-OpenSSH/releases/latest/) build. To get links to latest downloads - see [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/How-to-retrieve-links-to-latest-packages). A [Chocolatey package](https://chocolatey.org/packages/openssh) is also available if you prefer. For compat issues on Nano see [issues on Nano](https://github.com/PowerShell/Win32-OpenSSH/issues/234). See [here](https://github.com/PowerShell/Win32-OpenSSH/issues/332) for PS script to automate installation and configuration.
* Extract contents to `C:\Program Files\OpenSSH`
* Start Powershell as Administrator
     * `cd 'C:\Program Files\OpenSSH'`
* On Windows 10, in developer mode, a different implementation of SSH exists in-box. Look for TCP port bindings on port 22 and these processes - “SSH Server Broker” and “SSH Server Proxy”
     * `netstat -anop TCP`
     * If you do see 22 occupied, [#610](https://github.com/PowerShell/Win32-OpenSSH/issues/610) has workarounds to deal with port conflict. 
* Install sshd and ssh-agent services. 
     * `powershell -executionpolicy bypass -file install-sshd.ps1`
* Setup SSH host keys (this will generate all the 'host' keys that sshd expects when its starts)
     * `.\ssh-keygen.exe -A`
* Grant "NT service\sshd" read access the host private key files:
     ```
        Get-ChildItem -Path 'C:\Program Files\OpenSSH\ssh_host_*_key' | % {    
        $acl = get-acl $_.FullName
        $ar = New-Object  System.Security.AccessControl.FileSystemAccessRule("NT Service\sshd", "Read", "Allow")
        $acl.SetAccessRule($ar)
        Set-Acl $_.FullName $acl
     }
     ```
* Secure SSH host keys (optional)
     * `Start-Service ssh-agent`
     * download psexec from [here](https://technet.microsoft.com/en-us/sysinternals/pstools)
     * launch cmd.exe as SYSTEM - `psexec.exe -i -s cmd.exe`
     * register host keys in above cmd.exe
     * `ssh-add ssh_host_dsa_key`
     * `ssh-add ssh_host_rsa_key`
     * `ssh-add ssh_host_ecdsa_key`
     * `ssh-add ssh_host_ed25519_key`
     * host private keys are now securely stored by ssh-agent, private key files can be removed at this point.
* Open Firewall
     * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`
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
* Uninstall
     * `powershell.exe -executionpolicy bypass -file uninstall-sshd.ps1`
