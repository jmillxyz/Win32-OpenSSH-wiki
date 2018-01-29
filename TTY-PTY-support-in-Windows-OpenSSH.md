# SETUP
## Windows SSH client to Linux/Unix/macOS (*nix) SSH server 
 
If you want to have the best experience while using utilities like vi, top, man, etc. while connected from a Windows SSH client to a *nix server, you should configure your terminal to use an xterm-like rendering mode.

Unfortunately, this rendering mode is only included with Windows 10. If you're not on Windows 10, you may have more luck using a third-party terminal emulator or console host like [Cmder](http://cmder.net/) or [ConEmu](https://conemu.github.io/).

1. Open a shell from which you want to use `ssh` (either `powershell` or `cmd`).
1. Right-click the application icon in the upper-left and the window and select `Properties`.
1. Ensure that `Use legacy console (requires relaunch)` is unchecked:

   ![image](https://cloud.githubusercontent.com/assets/23668037/23882278/cc062726-081c-11e7-8e4c-792da6af23b9.png)    
1. In the `Layout` tab, set the Screen Buffer width and Window Size width to >= 90:

   ![image](https://cloud.githubusercontent.com/assets/23668037/23882328/11ed4116-081d-11e7-87e0-757680ea3a74.png)

   All of these changes will persist within the same shortcut to `cmd` or `powershell`.
   If you want to change the defaults for *new* shortcuts, select `Defaults` in Step 2 instead of `Properties`.
1. Set the `TERM` environment variable to `xterm`:

   ```
   set TERM=xterm 
   ```
   
   In PowerShell you can also use the `$env:` namespace to edit this variable:
   
   ```powershell
   $env:TERM = 'xterm'
   ```
   
   Both of these methods will only persist for the current session.
   If you want this to happen every time you start PowerShell, you can leverage [PowerShell profiles](https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles).

   If the TERM environment variable is not found then the SSH client code sets the TERM as 'xterm-256color' for the duration of SSH session.

## Linux/Unix/macOS SSH client to Windows SSH server

1. Set the `TERM` environment variable to `xterm`:
   ```bash
   export TERM=xterm
   ```

## Windows SSH Client to Windows SSH server

1) Set the non-legacy console mode in the console properties,   
   Make sure you **uncheck** the "use legacy console"   
![image](https://cloud.githubusercontent.com/assets/23668037/23882278/cc062726-081c-11e7-8e4c-792da6af23b9.png)    

2) Set the Screenbuffer width, window size width to >= 90.   
![image](https://cloud.githubusercontent.com/assets/23668037/23882328/11ed4116-081d-11e7-87e0-757680ea3a74.png)

3) If your windows client machine is **windows 10 or above ** and if you have any issues,   

   a) exit the current ssh session (if any)     

   b) set the environment variable SSH_TERM_CONHOST_PARSER to 0    
           c:\test\> set SSH_TERM_CONHOST_PARSER=0    
           c:\test\> set SSH_TERM_CONHOST_PARSER (This should show "0")    

   c) start a new ssh session.    

   d) if you are NOT able to reproduce then it is the windows 10 console issue. Please let us know by filing an issue, so that we will update the known issues. FYI, the fix should come from the windows 10 console team but not from the SSH team.    

   e) if you are able to reproduce then file an issue.    


## Known windows 10 console issues (These will be fixed by the windows console team)
### When connected to unix ssh server   
a) vim command, intermittently the file is opened in the Replace mode.     
b) top command, clears the previous screen contents.    
c) window resize is not supported because of a bug in conhost.exe (conhost.exe is a windows 10 module to parse ANSI sequences).

### When connected to windows ssh server and using powershell    
a) while executing an unknown command (abcdef) in the powershell, the last line ( + FullyQualifiedErrorId : CommandNotFoundException) is not displayed on the console

# Implementation details
## PTY
Windows OS does not have inbuilt support for a [pseudoterminal](https://en.wikipedia.org/wiki/Pseudoterminal). 

Windows version of OpenSSH server implements a VT100 PTY by intercepting [Windows Console](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682055(v=vs.85).aspx) events. This is implemented in ssh-shellhost.exe, connected to sshd via [std i/o](https://en.wikipedia.org/wiki/Standard_streams). At a high level, ssh-shellhost.exe acts as an intermediary between sshd and a Windows console applications performing the following: 
  - interprets incoming VT100, processes and calls Windows Console IO
  - processes Windows Console (output) event queue, translates them to VT100 and spits out on stdout.

## TTY
Windows console previously did not have the capability to process any incoming terminal control codes. Support for xterm is added in Windows 10. 

To support downlevel platforms (down till Windows 7), a VT100 interpreter is implemented in Windows OpenSSH client (ssh.exe) to support typical TTY scenarios. Although this works well when talking to Windows targets, there are limitations and known issues when dealing with interactive applications on Unix like top, vi, etc. 


If you are using the openssh client,  
  a) Windows 10+ OS then windows console (cmd.exe/powershell) has the support to interpret the VT sequences received from the SSH server. We simply pass the raw VT sequences to the console (cmd.exe/powershell). If you are facing any issues then it should be fixed by the windows console team.   

     If you want to use the inbuilt VT100 ANSI parser (that's part of the openssh client) then set the environment variable "SSH_TERM_CONHOST_PARSER" to 0.

  b) Prior versions of windows 10 OS, the raw VT sequences will be parsed by the inbuilt VT100 ANSI parser that's part of the ssh.exe. We have fixed most of the issues. We have no plans to extend to VT100+ sequences.   

     If you want support for the VT100+ sequences then please upgrade to windows 10+ OS or use the third party tools like putty, Cygwin, etc.

## Testing  
We have tested different scenarios,   
1) Windows client (cmd.exe / powershell) to windows server   
2) Windows client (cmd.exe / powershell) to Linux server   
3) Linux client to windows server   
4) Third party ssh tools like putty/Cygwin to windows server   

While making any terminal code changes, please make sure below manual test cases are passed
1) All the arrow key functions should work properly (up, down, left and right arrows).
2) The backspace, delete, home, end functions keys should work properly.
3) "cls" should clear only the visible window (windows 10+ OS) / should clear entire buffer (prior versions of windows 10 OS).
4) When connected to unix ssh server, the basic commands like vi, top, man, clear, etc should work properly.    
