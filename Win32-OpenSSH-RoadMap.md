_This is a living document_

#### Reliability (March) - Status - done
The current implementation of the POSIX IO wrapper ("select" and "fd" IO) for Windows has some limitations. Specifically, since it spawns numerous threads (one for each set fd in fd_sets passed to select), it does not scale. The overhead of synchronization between these threads impacts performance and reliability. We are currently working on replacing this with one that takes advantage of the native asynchronous IO support available in Windows (Overlapped IO) - hoping that many of the random IO related issues being reported will be resolved with this change. 
#### Security (April, May) - Status - done
Our current implementation requires the SSH daemon to run as Local System with high privileges. We are working on moving to a more secure model that runs the daemon with the least privileges required (and most likely as Network Service). We will also address other Windows relevant security aspects including key-based authentication and secure credential/passphrase management.
#### Code Prep - Status - In progress
The goal of this port is to ultimately converge in OpenSSH's main repo. Over the next couple of months, we will progressively seek feedback from the the OpenSSH community, prepare and refactor code as needed, with a plan to integrate into the main repo around the middle of 2016.


#### Rough timeline on when the key tasks would be accomplished:

| Date          | Task                   | Comments  |
| ---           |---                     | ---       |
| 3/31/2016     | Reliability Enhancements |           |
| 5/15/2016     | Security Enhancements     |        |
| 8/15/2015     | Code Prep for integration |        |
| 9/15/2015     | Integration in main repo  |        |



The intention is to be close in feature parity with OpenSSH by 9/15, following OpenSSH features will however will NOT be ready by this deadline:
- VerifyHostKeyDNS
- Client ControlMaster
- Background ssh execution mode
- VPN forwarding
- PKCS based smart cards
- GSSAPI based authentication
- Host based authentication (needs security review closure)
- Authentication forwarding (needs security review closure)
- Compression

Following Windows specific feature/work items are not in scope for this deadline:
- MINGW build support
- Use MS CNG (instead of OpenSSL)
- Key management using MS CNG
- Xterm, VT220 terminal modes
- ETW Logging (syslog alternative in Windows)

If you are looking for a specific feature not in above list and not in [here](https://github.com/PowerShell/Win32-OpenSSH/labels/feature%20request), feel free to add it [issue list](https://github.com/PowerShell/Win32-OpenSSH/issues).

