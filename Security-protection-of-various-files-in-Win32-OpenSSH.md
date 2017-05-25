Various OpenSSH resource files are integral to secure working of both server and client stacks. Here we discuss how to protect these resources, how OpenSSH for Windows enforces permission checks and individual case studies on how to fix any permission related issues. 

2 fundamental reasons leading to the differences between how these permission checks work on Unix vs Windows:
- SuperUser on Unix maps to either [System (SY)](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684190(v=vs.85).aspx) or [AdministratorsGroup (AG)] on Windows. 
- Permission controlling in Windows is more granular than in Unix. 


Its important to understand the distinction between "AdministratorsGroup" and an admin user. A logged on admin user would typically run processes in [non-elevated](https://msdn.microsoft.com/en-us/library/windows/desktop/dn742497(v=vs.85).aspx) mode. Even though an admin user is part of AG, these non-elevated processes **do not have authority** to access resources that are locked only to AG. 

Any misconfigured permissions would manifest as an attention seeking log entry. Ex. if a private key is not protected, you'll see the following:
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions for 'ssh_host_dsa_key' are too open.
```

## Server side resources
### Host private key files
Host keys represent host's identity. To prevent unauthorized access to these files, host keys need to be owned by SY or AG. No other user should have access to host key files. Its recommended that host private keys be registered with ssh-agent. Otherwise, sshd service would require read access to these files. 
Following is a misconfigured host private key because 'otheruser' owns it and has access to the key. 
```
PS C:\>(get-acl .\ssh_host_dsa_key).owner
otheruser
PS C:\>icacls .\ssh_host_dsa_key
ssh_host_dsa_key   NT AUTHORITY\SYSTEM:(F)
                   BUILTIN\Administrators:(F)
                   otheruser:(R) 
```
Steps to fix these permissions
```
PS C:\>icacls .\ssh_host_dsa_key /setowner system
PS C:\>icacls .\ssh_host_dsa_key /remove otheruser
```
### authorized_keys
authorized_keys is an user associated file that represents a list of authorized public keys that could be used for (key-based) user authentication. Unauthorized access to this file compromises the associated user's account. This file should not be owned by not provide access to any other user. Note that sshd service needs access to authorized_keys for public key validation. 
Following is a misconfigured authorized key because 'otheruser1' has access to the file (through inheritance) and 'otheruser2' has access to this file (explicit permission). 
```
PS C:\>(get-acl .\users\thisuser\.ssh\authorized_keys).owner
thisuser
PS C:\>icacls .\users\thisuser\.ssh\authorized_keys
ssh_host_dsa_key   NT SERVICE\sshd:(R)
                   BUILTIN\Administrators:(F)
                   thisuser:(F) 
                   otheruser1:(IR)
                   otheruser2:(R)
```
Steps to fix these permissions - disable inheritance and remove access to otheruser*
```
PS C:\>icacls .\users\thisuser\.ssh\authorized_keys /inheritance:d
PS C:\>icacls .\users\thisuser\.ssh\authorized_keys /remove otheruser1
PS C:\>icacls .\users\thisuser\.ssh\authorized_keys /remove otheruser2
```

