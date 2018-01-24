## Install Win32 OpenSSH (test release)

1. Note [these considerations](https://github.com/PowerShell/Win32-OpenSSH/wiki/Various-Considerations) and [project scope](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope) first.
1. Download the [latest](https://github.com/PowerShell/Win32-OpenSSH/releases/latest/) build of OpenSSH.
To get links to latest downloads [this wiki page](https://github.com/PowerShell/Win32-OpenSSH/wiki/How-to-retrieve-links-to-latest-packages).
1. Extract contents of the latest build to `C:\Program Files\OpenSSH` (Make sure binary location has the write permissions to just SYSTEM, Administrator groups.)
1. In an elevated Powershell console, run the following
    * `powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1`
1. Open the firewall for sshd.exe to allow inbound SSH connections
    * `New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22`

    Note: `New-NetFirewallRule` is for Windows 2012 and above servers only. If you're on a client desktop machine (like Windows 10) or Windows 2008 R2 and below, try:

    ```
    netsh advfirewall firewall add rule name=sshd dir=in action=allow protocol=TCP localport=sshd
    ```
1. Start `sshd` (this will automatically generate host keys under %programdata%\ssh if they don't already exist)
    * `net start sshd`
1. Migrate sshd configuration from 0.0.X.X versions (optional):
    * To use existing customized sshd_config, you need to copy it from binary location to %programdata%\ssh\sshd_config (Note that %programdata% is a hidden directory).
    * To use existing host keys, you need to copy them from binary location to %programdata%\ssh\
    * Prior versions required SSHD resources (sshd_config, host keys and authorized_keys) to have READ access to "NT Service\SSHD". This is no longer a requirement and the corresponding ACL entry should be removed. You may run `Powershell.exe -ExecutionPolicy Bypass -Command '. .\FixHostFilePermissions.ps1 -Confirm:$false'` (Note the first "." is a call operator.) to fix up these permissions.
1. Setup `sshd` and `ssh-agent` to auto-start (optional)
    * `Set-Service sshd -StartupType Automatic`
    * `Set-Service ssh-agent -StartupType Automatic`

1. Configuring the default ssh shell (optional)

On the server side, configure the default ssh shell in the windows registry. 

`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShell` - Full path (case sensitive) of the shell executable

`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShellCommandOption` - The switch that the configured default shell requires to execute a command and immediately exit and return to the calling process. It is used for executing the remote ssh commands. _Example- ssh user@ip hostname_

If you are configuring the powershell.exe/cmd.exe/WSL-bash.exe as default ssh shell then you can ignore `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShellCommandOption`. Your registry should look like [this](https://user-images.githubusercontent.com/23668037/32013581-67206dca-b970-11e7-8820-fde658d302c1.png).

If you want to configure default shell (Ex- cygwin) other than powershell/cmd/WSL-bash then your registry should look like [this](https://user-images.githubusercontent.com/23668037/32015013-9e644cee-b974-11e7-8375-bf3d50f596df.png)

## Uninstall Win32 OpenSSH

* Start Windows Powershell as Administrator
* Navigate to the OpenSSH directory
    * `cd 'C:\Program Files\OpenSSH'`
* Run the uninstall script
    * `powershell.exe -ExecutionPolicy Bypass -File uninstall-sshd.ps1`

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh
[build13]: https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v0.0.13.0