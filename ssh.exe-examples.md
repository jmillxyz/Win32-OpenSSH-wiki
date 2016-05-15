
##### Login With Password:
1. Work Group Users:
     * `ssh.exe user@host`
2. Domain Users:
     * `ssh.exe -l user@domain host`

##### Login With SSH Keys

1. Generate a key pair on the client:
     * `ssh-keygen.exe -t rsa -f id_rsa`
2. Register private key with ssh-agent (for single sign-on experience)
     * `net start ssh-agent`
     * `ssh-add id_rsa` 
3. Copy `id_rsa.pub` (client's public key) to corresponding user's directory on ssh HOST
     * as `%systemdrive%\users\user\\.ssh\authorized_keys`
4. Login using private key
     * `ssh.exe -i .\id_rsa user@host` (work group user)
     * `ssh.exe -i .\id_rsa -l user@domain host` (domain user)

##### Running Powershell

Powershell can be launched over SSH by running the command `powershell -File -` as follows:

    C:\Master>ssh.exe -l user@127.0.0.1
    user@127.0.0.1's password: **********
    Microsoft Windows [Version 10.0.10566]
    (c) 2016 Microsoft Corporation. All rights reserved.
    user@DEV-10566-829 C:\Users\user>powershell -File -
    PS C:\Users\user> get-module
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Checkpo...

##### For Unix and Linux users

The [Modern Unix Rosetta Stone](https://certsimple.com/rosetta-stone) includes Powershell examples of common Unix and Linux commands. 