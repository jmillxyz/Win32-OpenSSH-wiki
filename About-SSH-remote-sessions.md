Win32-OpenSSH currently supports password and key based authentication. Both local (workgroup) and domain accounts are supported. For domain accounts it is necessary to specify the domain as part of user name. See [wiki](https://github.com/PowerShell/Win32-OpenSSH/wiki/ssh.exe-examples) for different supported formats. 

### Double Hop differences between password and keybased auth
- A remote session opened via password authentication has the user credentials attached to it and hence is capable of outbound authentication as the user. 
- A remote session opened via key based authentication does not have associated user credentials and hence is **not** capable of outbound authentication as the user. This is by design and goes by the rules of standard Windows security. 





