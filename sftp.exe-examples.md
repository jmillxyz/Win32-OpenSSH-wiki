1. Setup sftp-server on host
       * Add location of sftp-server.exe binary as a subsystem in sshd_config file by adding an entry like below:
       * `Subsystem       sftp    d:\win32openssh\sftp-server.exe`
       * Restart sshd.exe
       * sftp-server will now be started automatically by sshd server when a sftp client connects to this computer

2. sftp.exe examples
     * `sftp test1@localhost          //login to local machine using test1 username`
     * `sftp -i .ssh/id_rsa test1@remotehost       //login using .ssh/id_rsa public key pair`
     * `pwd     //show current remote directory`
     * `lpwd     //show current local directory`
     * `cd /tests       //changed to remote directory c:/tests`
     * `get file1.txt   //download file1.txt from remote system to local directory`
     * `put file1.txt   //upload the file to remote system from the current local directory`
     * `exit`

