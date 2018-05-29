Listed here are Windows specific details that supplement or override the original sshd configuration manual documented in [OpenBSD manual](https://man.openbsd.org/sshd_config)

line1 
line2
_______
#### AllowGroups
This keyword can be followed by a list of group name patterns, separated by spaces. If specified, login is allowed only for users whose primary group or supplementary group list matches one of the patterns. Only group names are valid; a numerical group ID is not recognized. By default, login is allowed for all groups. The allow/deny directives are processed in the following order: **DenyUsers**, **AllowUsers**, **DenyGroups**, and finally **AllowGroups**. See PATTERNS in [ssh_config](http://man.openbsd.org/ssh_config.5) for more information on patterns.

windows specific info to follow...
_______
#### AllowStreamLocalForwarding
Not supported
______
#### AllowUsers
This keyword can be followed by a list of user name patterns, separated by spaces. If specified, login is allowed only for user names that match one of the patterns. Only user names are valid; a numerical user ID is not recognized. By default, login is allowed for all users. If the pattern takes the form USER@HOST then USER and HOST are separately checked, restricting logins to particular users from particular hosts. HOST criteria may additionally contain addresses to match in CIDR address/masklen format. The allow/deny directives are processed in the following order: **DenyUsers**, **AllowUsers**, **DenyGroups**, and finally **AllowGroups**. See PATTERNS in [ssh_config](http://man.openbsd.org/ssh_config.5) for more information on patterns.

windows specific info to follow...
______
#### AuthenticationMethods
Available authentication methods are "password" and "publickey".
______
#### Not supported
AuthorizedKeysCommand
AuthorizedKeysCommandUser
AuthorizedPrincipalsCommand
AuthorizedPrincipalsCommandUser

ChrootDirectory

Compression

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







