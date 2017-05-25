Various OpenSSH resource files are integral to secure working of both server and client stacks. Here we discuss how to protect these resources, how OpenSSH for Windows enforces permission checks and tips on how to fix any permission related issues. 

The are 2 fundamental reasons leading to the differences between how these permission checks work on Unix vs Windows.
- SuperUser on Unix maps to either [System (SY)](https://msdn.microsoft.com/en-us/library/windows/desktop/ms684190(v=vs.85).aspx) or [AdministratorsGroup (AG)] on Windows. 
- Permission controlling in Windows is more granular than in Unix. 

Its important to understand the distinction between "AdministratorsGroup" and an admin user. A logged on admin user would typically run processes in [non-elevated](https://msdn.microsoft.com/en-us/library/windows/desktop/dn742497(v=vs.85).aspx) mode. Even though an admin user is part of AG, these non-elevated processes do have authority to access resources that are locked only to AG. 

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
ssh_host_dsa_key   NT SERVICE\sshd:(R)
                   BUILTIN\Administrators:(F)
                   otheruser:(R) 
```
Steps to fix these permissions
```
PS C:\>icacls .\ssh_host_dsa_key /setowner system
PS C:\>icacls .\ssh_host_dsa_key /remove otheruser
```


# Secure protection of keys and config files

Starting with the release of [v0.0.13.0][build13], Win32-OpenSSH ensures any configuration and key files are secure before they are loaded.

Specifically, following permission checks are enforced:
### User specific resources on client side - private keys and ssh_config (%userprofile%\\.ssh\config)
  - Should be owned by the user
  - Should not be accessible to other users.
  - Ex. ssh would fail to use the following private key for userA, since "someotheruser" also has access.
```
c:\>icacls userkey
userkey  userA:(F)
         someotheruser:(R) 
```
### User specific resources on server side - authorized_keys
  - Should be owned by the user.
  - Should not be accessible to other users. 
  - "NT Service/sshd" can only have (R) access. 
  - Ex. sshd would not respect the following authorized_keys for userA, since "someotheruser" also has access. 
```
c:\>icacls authorized_keys 
authorized_keys   NT SERVICE\sshd:(R)
                  userA:(F)
                  someotheruser:(R) 
```
### Host specific resources on server side - host private keys 
In a secure configuration, host private keys should be registered with ssh-agent. See [wiki](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH) for details on how to secure host keys. If the private keys are directly consumed by SSHD, following are enforced:
  - Should be owned by "SYSTEM" (or Administrators group)
  - Should not be accessible to other users or groups (other than Administrators group).
  - "NT Service/sshd" can only have (R) access.
  - Ex. sshd would not respect the following host key, since "nonadmin" has access. 
```
c:\>icacls hostkey 
hostkey   NT SERVICE\sshd:(R)
          BUILTIN\Administrators:(F)
          nonadmin:(R) 
```

## Tips to adjust permissions
### icacls
You could use icacls to add / remove permissions on a given file
Ex. you could do the following to fix permissions on user's private key file
```
c:\>icacls userkey
userkey  userA(F)
         someotheruser(R) 
c:>icacls userkey /remove someotheruser
c:\>icacls userkey
userkey  userA(F)
```

You could also user the following PowerShell based routines to help with adjusting permissions
### Set-SecureFileACL

`Set-SecureFileACL` removes inherited ACLs on a file, assigns the current user as an owner (unless the `-Owner` parameter is specified), and grants the owner Full Control of the file:

```powershell
function Set-SecureFileACL
{
    param(
        [string]$FilePath,
        [System.Security.Principal.NTAccount]$Owner = $null
        )

    $myACL = Get-ACL -Path $FilePath
    $myACL.SetAccessRuleProtection($True, $FALSE)
    Set-Acl -Path $FilePath -AclObject $myACL

    $myACL = Get-ACL $FilePath
    $actualOwner = $null
    if($owner -eq $null)
    {
        $actualOwner = New-Object System.Security.Principal.NTAccount($($env:USERDOMAIN), $($env:USERNAME))
    }
    else
    {
        $actualOwner = $Owner
    }

    $myACL.SetOwner($actualOwner)

    if($myACL.Access)
    {
        $myACL.Access | % {
            if(-not ($myACL.RemoveAccessRule($_)))
            {
                throw "failed to remove access of $($_.IdentityReference.Value) rule in setup "
            }
        }
    }

    $objACE = New-Object System.Security.AccessControl.FileSystemAccessRule `
        ($actualOwner, "FullControl", "None", "None", "Allow")
    $myACL.AddAccessRule($objACE)
    Set-Acl -Path $FilePath -AclObject $myACL
}
```

#### Example: Setting the owner of `authorized_keys`

```powershell
$user = "<myusername>"
$objUser = New-Object System.Security.Principal.NTAccount($user)
Set-SecureFileACL -filepath $env:systemdrive\Users\$user\.ssh\authorized_keys -owner $objUser
```

### Add-PermissionToFileACL

`Add-PermissionToFileACL` grants an user a file permission to access a file.

```powershell
function Add-PermissionToFileACL
{
    param(
        [string]$FilePath,
        [System.Security.Principal.NTAccount] $User,
        [System.Security.AccessControl.FileSystemRights]$Perm)

    $myACL = Get-ACL $filePath
    $objACE = New-Object System.Security.AccessControl.FileSystemAccessRule `
        ($User, $perm, "None", "None", "Allow")
    $myACL.AddAccessRule($objACE)
    Set-Acl -Path $filePath -AclObject $myACL
}
```

#### Example: Grants `NT Service\sshd` Read permission to a host public key file

```powershell
Add-PermissionToFileACL -FilePath "$hostKeyFilePath -User "NT Service\sshd" -Perm "Read"
```

## Managing keys and config files end-to-end for Win32-OpenSSH

### Generating new keys using `v0.0.13.0`

If you've generated your host or user keys with `ssh-keygen.exe` after build [v0.0.13.0][build13], the user you've used to generated them will be the owner and have Full Control access.
However, some files will still require some ACL modification.

1. If the generated keys (both private and public) are going to be used as host keys, you must grant `NT Service\sshd` Read access: 
```powershell
Add-PermissionToFileACL -FilePath $hostKeyFilePath -User "NT Service\sshd" -Perm "Read"
Add-PermissionToFileACL -FilePath "$hostKeyFilePath.pub" -User "NT Service\sshd" -Perm "Read"
```

2. On the server running `sshd`, grant `NT Service\sshd` Read access to `authorized_keys` in `~\.ssh\`:
```powershell
$user = '<myusername>'
$userProfilePath = "$env:systemdrive\Users\$user"
Add-PermissionToFileACL -FilePath "$userProfilePath\.ssh\authorized_keys" -User "NT Service\sshd" -Perm "Read"
```

3. On the client machine, if an user has a `ssh_config` at `~\.ssh\config`, make sure that the user is the owner and has Full Control:
```powershell
Set-SecureFileACL '~\.ssh\config'
```

### Transitioning existing keys and files to `v0.0.13.0`

If you have host or user keys that were generated before build [v0.0.13.0][build13], you'll need to secure those key files before using them `v0.0.13.0` or later.

The keys generated by `ssh-keygen.exe` before [v0.0.13.0][build13] inherit permissions from the parent folder.
That means that some disallowed accounts may also have access to the file.

1. On the server running `sshd`, change the file permission of the private host key to set the current user as owner and grant current user Full Control and `NT Service\sshd` Read access. 
```powershell
Set-SecureFileACL -FilePath $hostPrivateKeyFilePath
Add-PermissionToFileACL -FilePath $hostPrivateKeyFilePath -User "NT Service\sshd" -Perm "Read"
```

2. On the server running `sshd`, grant `NT Service\sshd` Read access to the public host key:
```powershell
Add-PermissionToFileACL -FilePath $hostPublicKeyFilePath -User "NT Service\sshd" -Perm "Read"
```

3. Before using a user key file with `ssh-add`, `scp`, `ssh`, or `sftp`, make sure that the file is owned by the user, and that the user has Full Control.
```powershell
Set-SecureFileACL -FilePath $userPrivateKeyFilePath
```

4. On the server running `sshd`, change the file permission of `authorized_keys` in a user's home directory to set the current user as owner and grant the current user Full Control and `NT Server\sshd` Read access.
```powershell
$user = '<myusername>'
$userProfilePath = "$env:systemdrive\Users\<user>"
$objUser = New-Object System.Security.Principal.NTAccount($user)
Set-SecureFileACL "$userProfilePath\.ssh\authorized_keys" -owner $objUser
Add-PermissionToFileACL -FilePath "$userProfilePath\.ssh\authorized_keys" -User "NT Service\sshd" -Perm "Read"
```

5. On the client, if a user has their own `ssh_config` located at `~\.ssh\config`, make sure that the user is the owner and has Full Control:
```powershell
Set-SecureFileACL "~\.ssh\config"
```

[build13]: https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v0.0.13.0
