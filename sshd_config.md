Listed here are Windows specific details that supplement or override the original sshd configuration manual documented in [OpenBSD manual](https://man.openbsd.org/sshd_config). If you don't see a configuration entry here, the original man page reference holds true.

On Windows, [sshd](https://man.openbsd.org/sshd) reads configuration data from %programdata%\ssh\sshd_config (or the file specified with -f on the command line). If this file is absent, sshd will generate one with the default configuration on a service start.
_______
#### [AllowGroups](https://man.openbsd.org/sshd_config#AllowGroups), [AllowUsers](https://man.openbsd.org/sshd_config#AllowUsers), [DenyGroups](https://man.openbsd.org/sshd_config#DenyGroups), [DenyUsers](https://man.openbsd.org/sshd_config#DenyUsers)
The allow/deny directives are processed in the following order: **DenyUsers**, **AllowUsers**, **DenyGroups**, and finally **AllowGroups**. 

See PATTERNS in [ssh_config](http://man.openbsd.org/ssh_config.5#PATTERNS) for more information on patterns.

User and group names are case insensitive in Windows (unlike in Unix). You **should** always use **lower** case while specifying these irrespective of their original case.  

**Note** the following for domain accounts:

Prior to v7.7.0.0, there was no well defined way to specify domain principals (users and groups). To account for a domain principal in [various forms](https://msdn.microsoft.com/en-us/library/windows/desktop/ms724268(v=vs.85).aspx), it is recommended to use the following format while configuring user/group based rules - `user?domain*` - note the `?` instead of `@` to avoid conflict with `username@host` format and `*` added to cover FQDNs.

From v7.7.0.0 on wards, work group users/groups and internet-connected accounts are strictly resolved to their local account name (no domain part, similar to standard Unix names). Domain users and groups are strictly resolved to NameSamCompatible format - domain_short_name\user_name. All user/group based configuration rules need to adhere to this format. 

- Ex. for domain users and groups
  - `DenyUsers contoso\admin@192.168.2.23` : blocks contoso\admin from 192.168.2.23
  - `DenyUsers contoso\*`  : blocks all users from contoso domain
  - `AllowGroups contoso\sshusers` : only allow users from contoso\sshusers group
- Ex. for local users and groups
  - `AllowUsers localuser@192.168.2.23`
  - `AllowGroups sshusers`

**Note that user and group names are in lower case**

______
#### [AuthenticationMethods](https://man.openbsd.org/sshd_config#AuthenticationMethods)
Available authentication methods are "password" and "publickey".
______
#### [AuthorizedKeysFile](https://man.openbsd.org/sshd_config#AuthorizedKeysFile)
The default is “.ssh/authorized_keys .ssh/authorized_keys2”. If the path is not absolute, it is taken relative to user's home directory (or profile image path). Ex. c:\users\user.
______
#### [ChrootDirectory](https://man.openbsd.org/sshd_config#ChrootDirectory)
Support added in v7.7.0.0

To setup a sftp-only chroot server, set ForceCommand to `internal-sftp`. You may also set up scp with chroot, by implementing a custom shell that would only allow scp and sftp. 
_____
#### [HostKey](https://man.openbsd.org/sshd_config#HostKey)
The defaults are _%programdata%/ssh/ssh_host_ecdsa_key_, _%programdata%/ssh/ssh_host_ed25519_key_ and _%programdata%/ssh/ssh_host_rsa_key_. If the defaults are not present, sshd will automatically generate these on a service start.
______
#### [Match](https://man.openbsd.org/sshd_config#Match)
Note that pattern rules in [this](https://github.com/PowerShell/Win32-OpenSSH/wiki/sshd_config#allowgroups-allowusers-denygroups-denyusers) section. User and group names should be in **lower** case.
______
#### [PermitRootLogin](https://man.openbsd.org/sshd_config#PermitRootLogin)
Not applicable in Windows. To prevent administrator login, use _Administrators_ with DenyGroups directive.
______
#### [SyslogFacility](https://man.openbsd.org/sshd_config#SyslogFacility)
If you need file based logging, use LOCAL0. Logs will be generated under %programdata%\ssh\logs.

Any other value, including the default value AUTH directs logging to ETW. For more info see [Logging Facilities in Windows](https://github.com/PowerShell/Win32-OpenSSH/wiki/Logging-Facilities).
______
#### Not supported
AcceptEnv

AllowStreamLocalForwarding

AuthorizedKeysCommand

AuthorizedKeysCommandUser

AuthorizedPrincipalsCommand

AuthorizedPrincipalsCommandUser

Compression

ExposeAuthInfo

GSSAPIAuthentication

GSSAPICleanupCredentials

GSSAPIStrictAcceptorCheck

HostbasedAcceptedKeyTypes

HostbasedAuthentication

HostbasedUsesNameFromPacketOnly

IgnoreRhosts

IgnoreUserKnownHosts

KbdInteractiveAuthentication

KerberosAuthentication

KerberosGetAFSToken

KerberosOrLocalPasswd

KerberosTicketCleanup

PermitRootLogin

PermitTunnel

PermitUserEnvironment

PermitUserRC

PidFile

PrintLastLog

RDomain

StreamLocalBindMask

StreamLocalBindUnlink

StrictModes

X11DisplayOffset

X11Forwarding

X11UseLocalhost

XAuthLocation







