
## Login With Password

1. Workgroup users
     * `ssh user@host`
2. Domain users: Domain needs to be explicitly specified. Any of the following formats work
     * `ssh -l user@domain host`
     * `ssh domain\user@host`
     * `ssh user@domain@host`

## Login With SSH Keys

### Usage from client-side (`ssh`)

1. Generate a key pair on the client (preferably with a passphrase):
     * `ssh-keygen -t rsa -f id_rsa`
2. Register private key with ssh-agent (optional, for single sign-on experience)
     * `net start ssh-agent`
     * `ssh-add id_rsa` 
3. Login using private key
     * `ssh -i .\id_rsa user@host` (workgroup user)
     * `ssh -i .\id_rsa -l user@domain host` (domain user)

### Setup server-side (`sshd`)

1. Copy `id_rsa.pub` (client's public key) to corresponding user's directory on the SSH server at `%systemdrive%\Users\<user>\.ssh\authorized_keys`
2. Double check permission on authorized_keys (only System, Administrators and owner can have access).

### For Unix and Linux users

The [Modern Unix Rosetta Stone](https://certsimple.com/rosetta-stone) includes PowerShell examples of common Unix and Linux commands. 

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh
