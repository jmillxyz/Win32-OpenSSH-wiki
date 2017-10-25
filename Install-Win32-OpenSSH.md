## Install Win32 OpenSSH (test release)

1. Note [these considerations](https://github.com/PowerShell/Win32-OpenSSH/wiki/Various-Considerations) and [project scope](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope) first.
1. Download the [latest](https://github.com/PowerShell/Win32-OpenSSH/releases/latest/) build of OpenSSH.
To get links to latest downloads [this wiki page](https://github.com/PowerShell/Win32-OpenSSH/wiki/How-to-retrieve-links-to-latest-packages).
A [Chocolatey package](https://chocolatey.org/packages/openssh) is also available if you prefer using Chocolatey.
For compatibility issues on Nano see [issues on Nano](https://github.com/PowerShell/Win32-OpenSSH/issues/234).
If you're interested in a PowerShell script to automate installation and configuration check out [this issue](https://github.com/PowerShell/Win32-OpenSSH/issues/332).
1. Extract contents of the latest build to `C:\Program Files\OpenSSH`
1. Start Windows Powershell as Administrator
1. Navigate to the OpenSSH directory
    * `cd 'C:\Program Files\OpenSSH'`
1. On Windows 10, if you've [enabled Developer Mode](https://docs.microsoft.com/en-us/windows/uwp/get-started/enable-your-device-for-development), you probably have another implementation of SSH installed on your machine.
To figure out if this is the case, look for TCP port bindings on port 22 and these services: “SSH Server Broker” and “SSH Server Proxy”
    * `netstat -anop TCP`
    * If you do see 22 occupied, [#610](https://github.com/PowerShell/Win32-OpenSSH/issues/610) has workarounds to deal with port conflict. 
1. Install `sshd` and `ssh-agent` services. 
    * `powershell -ExecutionPolicy Bypass -File install-sshd.ps1`
1. Generate SSH host keys
    * `.\ssh-keygen.exe -A`
    * `.\FixHostFilePermissions.ps1 -Confirm:$false`
1. Secure SSH host keys (optional)
    * `Start-Service ssh-agent`
    * Download psexec from [here](https://technet.microsoft.com/en-us/sysinternals/pstools)
    * Launch cmd.exe as SYSTEM
        * `psexec.exe -i -s cmd.exe`
    * register host keys in above cmd.exe
        * `ssh-add ssh_host_dsa_key`
        * `ssh-add ssh_host_rsa_key`
        * `ssh-add ssh_host_ecdsa_key`
        * `ssh-add ssh_host_ed25519_key`
    * Host private keys are now securely stored by ssh-agent, private key files can be deleted at this point.
1. Open the firewall on TCP port 22 to allow inbound SSH connections
    * `New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH`

    Note: `New-NetFirewallRule` is for servers only. If you're on a client desktop machine (like Windows 10) try:

    ```
    netsh advfirewall firewall add rule name=SSHPort dir=in action=allow protocol=TCP localport=22
    ```
1. Setup `sshd` and `ssh-agent` to auto-start (optional)
    * `Set-Service sshd -StartupType Automatic`
    * `Set-Service ssh-agent -StartupType Automatic`
1. Configuring the default ssh shell (optional)

Configure the default ssh shell in the windows registry. 

`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShell` - Full path (case sensitive) of the shell executable

`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShellCommandOption` - command option for the default shell. It is used for executing the remote ssh commands. _Example- ssh user@ip hostname_

If you are configuring the powershell.exe/cmd.exe/bash.exe(WSL) as default ssh shell then you can ignore `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShellCommandOption`. Your registry should look like [this](https://user-images.githubusercontent.com/23668037/32013581-67206dca-b970-11e7-8820-fde658d302c1.png).

If you want to configure default shell (Ex- cygwin) other than powershell/cmd/WSL-bash then your registry should look like [this](https://user-images.githubusercontent.com/23668037/32015013-9e644cee-b974-11e7-8375-bf3d50f596df.png)

## Uninstall Win32 OpenSSH

* Start Windows Powershell as Administrator
* Navigate to the OpenSSH directory
    * `cd 'C:\Program Files\OpenSSH'`
* Run the uninstall script
    * `powershell.exe -ExecutionPolicy Bypass -File uninstall-sshd.ps1`

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh
[build13]: https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v0.0.13.0