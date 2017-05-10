
##### Login With Password:
1. Work Group Users:
     * `ssh user@host`
2. Domain Users: Domain needs to be explicitly specified. Any of the following formats would work:
     * `ssh -l user@domain host`
     * `ssh domain\user@host`
     * `ssh user@domain@host`


##### Login With SSH Keys
**Setup Server machine**
***
1. Copy `id_rsa.pub` (client's public key) to corresponding user's directory on ssh server machine
     * as `%systemdrive%\users\<user>\.ssh\authorized_keys` (path on the ssh server machine)
2. Adjust permissions on authorized_keys file
```   
   $authorizedKeyPath = "%systemdrive%\users\user\.ssh\authorized_keys"
   $acl = get-acl $authorizedKeyPath
   $ar = New-Object  System.Security.AccessControl.FileSystemAccessRule("NT Service\sshd", "Read", "Allow")
   $acl.SetAccessRule($ar)
   Set-Acl  $authorizedKeyPath $acl
``` 

**Usage from Client machine**
***

1. Generate a key pair on the client:
     * `ssh-keygen -t rsa -f id_rsa`
2. Register private key with ssh-agent (for single sign-on experience)
     * `net start ssh-agent`
     * `ssh-add id_rsa` 
3. Login using private key
     * `ssh -i .\id_rsa user@host` (work group user)
     * `ssh -i .\id_rsa -l user@domain host` (domain user)



##### For Unix and Linux users

The [Modern Unix Rosetta Stone](https://certsimple.com/rosetta-stone) includes Powershell examples of common Unix and Linux commands. 