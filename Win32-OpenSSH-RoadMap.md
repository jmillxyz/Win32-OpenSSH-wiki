_This is a living document_

Side by side comparison of OpenSSH 7.1 and the current Windows port features is listed here (will be linked soon).

The primary focus over the next 3-4 months is three fold:
#### Reliability
The current implementation of POSIX IO wrapper ("select" and "fd" IO) for Windows has limitations. Specifically, since it spawns numerous threads (one for each set fd in fd_sets passed to select), it does not scale. The overhead of synchronization between these threads impacts performance and reliability. We are currently working on replacing this with a one that takes advantage of the native asynchronous IO support available in Windows (Overlapped IO) - hoping that many of the random IO related issues being reported will be resolved with this change. 
#### Security
Current implementation requires SSH daemon to run as Local System with high privileges. We are working on moving to a more secure model that runs the daemon with the least privileges required (and most likely as Network Service). We will also address other Windows relevant security aspects including key-based authentication and secure credential/passphrase management.
#### Code Prep
Goal of this port is to ultimately converge in OpenSSH's main repo. Over the next couple of months, we will progressively seek OpenSSH's community's feedback, prepare and refactor code as needed, and plan on main repo's integration around mid year 2016.


#### Rough timeline on when the key tasks would be accomplished:

| Date          | Task                   | Comments  |
| ---           |---                     | ---       |
| 3/31/2016     | Reliability Enhancements |           |
| 4/30/2016     | Security Enhancements     |        |
| 5/31/2015     | Code Prep for integration |        |
| 6/30/2015     | Integration in main repo  |        |
