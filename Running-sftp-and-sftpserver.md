
• _SFTP client needs ssh client and SFTP server uses sshd server. So first setup ssh client and server using the wiki instructions._


## # Setup sftp-server
1. stop the sshd server by doing steps like "net stop openssh"
2. Add location of sftp-server.exe binary as a subsystem in sshd_config file by adding entries like below:
`Subsystem       sftp    /win32openssh/bin/sftp-server.exe`
3. start the sshd server by doing steps like "net start openssh"
4. sftp-server will now be started automatically by sshd server when a sftp client connects to this computer

## # run Sftp client
`sftp test1@localhost          //login to local machine using test1 username`

`sftp -i .ssh/id_rsa test1@remotehost       //login using .ssh/id_rsa public key pair`

`pwd     //show current remote directory`

`lpwd     //show current local directory`

`cd /tests       //changed to remote directory c:/tests`

`get file1.txt   //download file1.txt from remote system to local directory`

`put file1.txt   //upload the file to remote system from the current local directory`

`exit`

