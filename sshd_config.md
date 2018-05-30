Listed here are Windows specific details that supplement or override the original sshd configuration manual documented in [OpenBSD manual](https://man.openbsd.org/sshd_config). If you don't see a configuration entry here, the original man page reference holds true.
_______
#### AllowGroups, AllowUsers, DenyGroups, DenyUsers
The allow/deny directives are processed in the following order: **DenyUsers**, **AllowUsers**, **DenyGroups**, and finally **AllowGroups**. 

See PATTERNS in [ssh_config](http://man.openbsd.org/ssh_config.5#PATTERNS) for more information on patterns.

Note the following for domain accounts:

Prior to v7.7.0.0, there was no well defined way to specify domain principals (users and groups). To account for a domain principal in [various forms](https://msdn.microsoft.com/en-us/library/windows/desktop/ms724268(v=vs.85).aspx), it is recommended to use the following format while configuring user/group based rules - `user?domain*` - note the `?` instead of `@` to avoid conflict with `username@host` format and `*` added to cover FQDNs.

From v7.7.0.0 on wards, work group users/groups and internet-connected accounts are strictly resolved to their local account name (no domain part, similar to standard Unix names). Domain users and groups are strictly resolved to NameSamCompatible format - domain_short_name\user_name. All user/group based configuration rules need to adhere to this format. 

- Ex. for domain users - `DenyUsers contoso\admin@192.168.2.23`
- Ex. for local users  - `AllowUsers localuser@192.168.2.23`
______
#### AuthenticationMethods
Available authentication methods are "password" and "publickey".
______
#### ChrootDirectory
Support added in v7.7.0.0

To setup a sftp-only chroot server, set ForceCommand to `internal-sftp`. You may also set up scp with chroot, by implementing a custom shell that would only allow scp and sftp. 

______
#### Not supported
AcceptEnv

AllowStreamLocalForwarding

AuthorizedKeysCommand

AuthorizedKeysCommandUser

AuthorizedPrincipalsCommand

AuthorizedPrincipalsCommandUser

ChrootDirectory

Compression

DenyGroups

DenyUsers

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







