If sshd cannot start
  * Check that sshd_config is in place
  * Check that host keys are generated and have correct file permission:
  * Check if private host keys are protected:
    * The file owner of private host key files cannot be other accounts than current user, Administrators group, Local system account, or an user account in local Administrators group
    * Other accounts than current user, Administrators group, Local system account, or an user account in local Administrators group should not have any access to private key file
  * Check if "NT service\sshd" have read access to public key files

Trouble shooting steps for typical connection issues:
- Server side - run sshd in debug mode
   * `net stop sshd`
   * In an elevated admin console - `sshd.exe -d`
   * This will dump debug logs real time on console
- Client side - start ssh.exe in verbose mode
   * `ssh.exe -v ...`
   * This will dump verbose logs on console

Trouble shooting more complex issues:
- Server side
  * stop sshd and ssh-agent services
  * delete sshd.log and ssh-agent.log (in logs directory)
  * set LogLevel to DEBUG (or DEBUG2/DEBUG3 for higher levels of logging) in sshd_config
  * run the scenario. sshd.log and ssh-agent.log will contain sshd and ssh-agent related traces respectively
- Client side
  * set LogLevel to DEBUG (or DEBUG2/DEBUG3 for higher levels of logging) in ssh_config
  * Run ssh.exe in verbose mode as detailed above
