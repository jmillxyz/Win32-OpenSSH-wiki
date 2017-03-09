
##### Login With Password:
1. Work Group Users:
     * `ssh.exe user@host`
2. Domain Users:
     * `ssh.exe -l user@domain host`
     * If above doesn't work, try this
       - ssh.exe 'DOMAIN\user'@host 


##### Login With SSH Keys

1. Generate a key pair on the client:
     * `ssh-keygen.exe -t rsa -f id_rsa`
2. Register private key with ssh-agent (for single sign-on experience)
     * `net start ssh-agent`
     * `ssh-add.exe id_rsa` 
3. Copy `id_rsa.pub` (client's public key) to corresponding user's directory on ssh HOST
     * as `%systemdrive%\users\user\.ssh\authorized_keys`
4. Login using private key
     * `ssh.exe -i .\id_rsa user@host` (work group user)
     * `ssh.exe -i .\id_rsa -l user@domain host` (domain user)

##### For Unix and Linux users

The [Modern Unix Rosetta Stone](https://certsimple.com/rosetta-stone) includes Powershell examples of common Unix and Linux commands. 