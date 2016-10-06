Intent of this project is to get to a state that can converge to and integrate into OpenSSH's main repo. To simplify this integration, following features have been scoped out and will not work on Windows yet:
- VerifyHostKeyDNS
- Client ControlMaster
- Background ssh execution mode
- VPN forwarding
- PKCS based smart cards
- GSSAPI based authentication
- Host based authentication
- Authentication forwarding
- Compression
- ProxyCommand
(Windows specific feature/work items)
- MINGW build support
- Use MS CNG (instead of OpenSSL)
- Key management using MS CNG
- Xterm, VT220 terminal modes
- ETW Logging (syslog alternative in Windows)

If you are looking for a specific feature not in above list and not in [here](https://github.com/PowerShell/Win32-OpenSSH/labels/feature%20request), feel free to add it [issue list](https://github.com/PowerShell/Win32-OpenSSH/issues).

