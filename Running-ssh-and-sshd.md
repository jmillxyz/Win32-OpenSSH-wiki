- copy zlib1.dll from zlib source directory to openssh directory

### generate ssh.exe client keys if note done before
ssh-keygen.exe -t rsa -f id_rsa

ssh-keygen.exe -t dsa -f id_dsa

ssh-keygen.exe -t ecdsa -f id_ecdsa

### generate sshd.exe server keys if not done before
ssh-keygen.exe -t rsa -f ssh_host_rsa_key

ssh-keygen.exe -t dsa -f ssh_host_dsa_key

ssh-keygen.exe -t ecdsa -f ssh_host_ecdsa_key

### generate empty sshd_config file if there is no sshd_config file before
echo #sshd_config file > sshd_config

### test sshd.exe can run
./sshd.exe -r

### test ssh.exe client can run
./ssh.exe -V

#### Running SSH server:

1. Download Service Manager tool NSSM 

[https://nssm.cc/download](https://nssm.cc/download)

2. Unzip the download and run the follwoing command as Administrator form the uncompressed directory,

	nssm.exe install

3. In the NSSM UI set the following values to install the service
	
set service name 'openssh' 

set Path to the location of sshd.exe

set Startup directory to the directory where sshd.exe exists

set arguments -r

Install service

3. run the follwoing command as Administrator,

	net start openssh
	
	
	
#### Running ssh.exe:
##### Login With Password:

Launch a cmd shell and go to the directory where ssh.exe exists.

for local users:

./ssh.exe user@host

for domain user:

./ssh.exe -l user@domain host

##### Login with Certificate:
Launch a cmd shell and go to the directory where ssh.exe exists. Run

./ssh-keygen -t rsa

to generate rsa certificate, lets say with the file names are id_rsa and id_rsa.pub, located in the same directory as ssh.exe 

append the contents of id_rsa.pub file to authorized_keys file located in the .ssh directory under remote user's home direcotry in the reomote host. Then run:

./ssh.exe -i ./id_rsa user@host

for domain users:

./ssh.exe -i ./id_rsa -l user@domain host