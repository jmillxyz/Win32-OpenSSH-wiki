1. Download [Zip file](https://github.com/PowerShell/Win32-OpenSSH/releases/download/10_13_2015/OpenSSH-Win32.zip)
* Extract contents
* Setup SSH host keys (with no passphrase)
     * ssh-keygen.exe -t rsa -f ssh_host_rsa_key
     * ssh-keygen.exe -t dsa -f ssh_host_dsa_key
     * ssh-keygen.exe -t ecdsa -f ssh_host_ecdsa_key
     * ssh-keygen.exe -t ed25519 -f ssh_host_ed25519_key
* Open Firewall
     * new-netfirewallrule -Name SSH -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH
* Install key-auth package if you need key-based authentication
     * run setup-ssh-lsa.cmd
     * reboot
* Run SSH daemon as System (See below for alternative options)
     * Download PSTools from [SysInternals](https://technet.microsoft.com/en-us/sysinternals/bb897553)
     * psexec.exe -i -s cmd.exe
     * Within cmd.exe - launch sshd.exe
* Running SSH daemon as Admin user
     * Note - SSH daemon needs to run as System to support key-based authentication
     * Give Admin user SE_ASSIGNPRIMARYTOKEN_NAME (steps below)
     * secpol.msc -> Local Policies -> UserRightsAssessment  
     * Add the Admin user to "Replace a process level token"
     * Log off and Log in.
     * In elevated cmd.exe, start sshd.exe
