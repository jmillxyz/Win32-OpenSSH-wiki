To keep the file path difference issues between windows and Unix simple, we used the following directory structure for our development and testing. We recommend you do the same. 

![](https://github.com/PowerShell/Win32-OpenSSH/blob/master/images/test_directory_structure.png)


Please follow the build steps mentioned in this article to get regular build working first.


[Build Instructions](https://github.com/PowerShell/Win32-OpenSSH/wiki/OpenSSH-32-bit-Build-and-Installation-Instructions)


Run the following command to build the necessary ssh tools.

`make`

Run the following command to start testing

`make tests`





NOTES: All the openssh tests can be roughly classified in these three categories:
* Unit tests written in c language that tests the algorithm parts
* ssh-keygen related tests
* *.sh shell script tests

Right now we are running the first two categories and have good coverage. We looking into the *.sh shell scripts to see how much of it makes sense to port in windows
