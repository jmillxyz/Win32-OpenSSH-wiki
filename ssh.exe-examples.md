
##### Login With Password:
1. Work Group Users:
     * ssh.exe user@host
2. Domain Users:
     * ssh.exe -l user@domain host

##### Login With Client Keys (key-based authentication)
1. Generate client authentication key
     * ssh-keygen.exe -t rsa -f id_rsa
2. Copy id_rsa.pub (client's public key) to corresponding user's directory on ssh HOST
     * as %windir%\users\user\\.ssh\authorized_keys
3. Login using private key
     * ./ssh.exe -i ./id_rsa user@host (work group user)
     * ./ssh.exe -i ./id_rsa -l user@domain host (domain user)