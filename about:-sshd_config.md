Listed here are Windows specific details that supplement or override the original sshd configuration manual documented in [OpenBSD manual](https://man.openbsd.org/sshd_config)
_______
#### AllowGroups, AllowUsers, DenyGroups, DenyUsers
The allow/deny directives are processed in the following order: **DenyUsers**, **AllowUsers**, **DenyGroups**, and finally **AllowGroups**. 

See PATTERNS in [ssh_config](http://man.openbsd.org/ssh_config.5) for more information on patterns.

windows specific info to follow...
______
#### AuthenticationMethods
Available authentication methods are "password" and "publickey".
______
#### Chroot
fds
______
#### Not supported
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







