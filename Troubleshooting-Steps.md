See [Logging Facilities](https://github.com/PowerShell/Win32-OpenSSH/wiki/Logging-Facilities) to modify logging location.

Troubleshooting steps for typical service startup and connection issues:

* Server side: run `sshd` in debug mode
  * `Stop-Service sshd` (if sshd service is running)
  * Open cmd as SYSTEM. Download psexec from [here](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec).
    * `psexec -s cmd`
    * Note: sshd.exe in debug mode doesn't play well with psexec in interactive mode (psexec -i), don't use this option yet.
  * `sshd.exe -d`
  * This will dump debug logs in real time to stdout on the console
  * You can also add additional `d`s for more detailed loggin:
    * `sshd.exe -dd` or `sshd.exe -ddd`
* Client side: start `ssh` in verbose mode
  * `ssh.exe -v ...`
  * This will dump verbose logs in real time to stdout on the console
  * You can also add additional `v`s to get more verbose messages:
    * `ssh.exe -vv ...` or `ssh.exe -vvv`

Troubleshooting more complex issues:
* Server side
  * Stop `sshd`
    * `Stop-Service sshd`
  * Delete `sshd.log` and `ssh-agent.log` (under %programdata%\ssh\logs)
  * Set the following in `sshd_config`
    * `SyslogFacility LOCAL0`
    * `LogLevel` to `DEBUG` (or `DEBUG2`/`DEBUG3` for higher levels of logging)
  * Rerun the workflow that's giving you problems. `logs\sshd.log` will contain `sshd` related traces.
  * If the problem isn't clear, please post these logs along with some steps to help us reproduce your problem in [our GitHub Issues](https://github.com/powershell/Win32-OpenSSH/issues).
* Client side
  * Set `LogLevel` to `DEBUG` (or `DEBUG2`/`DEBUG3` for higher levels of logging) in `ssh_config`. 
  * Run `ssh.exe` in verbose mode as detailed above

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh