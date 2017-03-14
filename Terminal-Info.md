**Overview**   

Windows OS doesn't provide the support for virtual terminal (or) interpret the VT sequences.

As part of the SSH connection establishment, SSH server will create a virtual terminal. SSH Client will transmit each and every keystroke to the virtual terminal at SSH Server side. SSH Server virtual terminal will in return sends back the VT sequences to SSH client. Windows SSH client will receive, interpret the VT sequences and renders the data sent by the SSH server.

If you are using the openssh client and using the windows cmd.exe/powershell to connect to the SSH server then the cmd.exe/powershell needs to interpret and render the VT sequences. Unfortunately cmd.exe/powershell doesn't have the ability to interpret the VT sequences until windows 10.

For more information, please go through https://msdn.microsoft.com/en-us/library/windows/desktop/mt638032(v=vs.85).aspx

If you are using the openssh client,  
  a) Using windows 10+ OS then windows console (cmd.exe/powershell) has the support to interpret the VT sequences received from the SSH server. We simply pass the raw VT sequences to the console (cmd.exe/powershell). If you are facing any issues then it should be fixed by the windows console team.   

     If you want to use the inbuilt VT100 ANSI parser (that's part of the openssh client) then set the environment variable "SSH_TERM_CONHOST_PARSER" to 0.

  b) Using prior versions of windows 10 OS, the raw VT sequences will be parsed by the inbuilt VT100 ANSI parser that's part of the openssh client. We have fixed most of the issues and have no plans to extend to VT100+ sequences.   

     If you want support for the VT100+ sequences then please upgrade to windows 10+ OS or use the third party tools like putty, Cygwin, etc.

**For UNIX users**   
If you want to use vi / top / man, etc commands while connecting from windows client to Linux server then please follow these steps   

1) Upgrade to windows 10+ OS.   

2) Set the non-legacy console mode in the console properties,   
![image](https://cloud.githubusercontent.com/assets/23668037/23882278/cc062726-081c-11e7-8e4c-792da6af23b9.png)    

3) Set the Screenbuffer width, window size width to >= 90.   
![image](https://cloud.githubusercontent.com/assets/23668037/23882328/11ed4116-081d-11e7-87e0-757680ea3a74.png)

4) Set the environment variable (TERM) to xterm. Before/After logging into the SSH server.

** Testing **   
We have tested different scenarios,   
1) Windows client (cmd.exe / powershell) to windows server   
2) Windows client (cmd.exe / powershell) to Linux server   
3) Linux client to windows server   
4) Third party ssh tools like putty/Cygwin to windows server   




