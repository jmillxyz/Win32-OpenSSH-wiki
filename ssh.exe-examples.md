
##### Login With Password:
1. Work Group Users:
     * ssh.exe user@host
2. Domain Users:
     * ssh.exe -l user@domain host

##### Login With Client Keys (key-based authentication)
1. Generate client authentication key
     * ssh-keygen.exe -t rsa -f id_rsa
2. Copy id_rsa.pub (client's public key) to corresponding user's directory on ssh HOST
     * as %systemdrive%\users\user\\.ssh\authorized_keys
3. Login using private key
     * ssh.exe -i .\id_rsa user@host (work group user)
     * ssh.exe -i .\id_rsa -l user@domain host (domain user)

##### Remote Powershell over ssh
Powershell can be launched over remote ssh session as follows:

`C:\Master>ssh.exe -l user@127.0.0.1`

`user@127.0.0.1's password: **********`

`Microsoft Windows [Version 10.0.10566]`

`(c) 2016 Microsoft Corporation. All rights reserved.`

`user@DEV-10566-829 C:\Users\user>powershell -File -`


`PS C:\Users\user> get-module`

`ModuleType Version    Name                                ExportedCommands`

`---------- -------    ----                                ----------------`

`Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Checkpo...`