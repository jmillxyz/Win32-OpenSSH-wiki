If sshd won't start:

* Ensure that `sshd_config` is in the same directory as `sshd.exe`
* Ensure that host keys are generated and have the [correct file permissions][Secure file]:
  * Ensure that private host keys are [secured][Secure file]
  * Ensure that `NT service\sshd` has `Read` access to public and private key files

Troubleshooting steps for typical connection issues:

* Server side: run `sshd` in debug mode
  * `net stop sshd`
  * In an elevated Administrator console, run `sshd` in debug mode
    * `sshd.exe -d`
  * This will dump debug logs in real time to stdout on the console
  * You can also add additional `d`s to get more debug information:
    * `sshd.exe -dd` or `sshd.exe -ddd`
* Client side: start `ssh` in verbose mode
  * `ssh.exe -v ...`
  * This will dump verbose logs in real time to stdout on the console
  * You can also add additional `v`s to get more verbose messages:
    * `ssh.exe -vv ...` or `ssh.exe -vvv`

Troubleshooting more complex issues:
* Server side
  * Stop `sshd` and `ssh-agent` services
    * `Get-Service ssh* | Stop-Service`
  * Delete `sshd.log` and `ssh-agent.log` (in the `logs` directory of your installation path)
  * Set `LogLevel` to `DEBUG` (or `DEBUG2`/`DEBUG3` for higher levels of logging) in `sshd_config`
  * Rerun the workflow that's giving you problems. `logs\sshd.log` and `logs\ssh-agent.log` will contain `sshd` and `ssh-agent` related traces respectively.
  * If the problem isn't clear, please post these logs along with some steps to help us reproduce your problem in [our GitHub Issues](https://github.com/powershell/Win32-OpenSSH/issues).
* Client side
  * Set `LogLevel` to `DEBUG` (or `DEBUG2`/`DEBUG3` for higher levels of logging) in `ssh_config`. 
  * Run `ssh.exe` in verbose mode as detailed above

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh