Win32-OpenSSH's Chocolatey package can be found [here](https://chocolatey.org/packages/win32-openssh). The package is authored and being maintained by [@DarwinJS](https://github.com/DarwinJS). It is typically updated within a couple of days of a new release on GitHub. 

The package automates the install steps listed in [wiki](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH). The package is also engineered to use 'Choco upgrade win32-openssh' to update to the latest release.
Be sure to look over the installation instructions on Chocolatey as the package takes special switches to install, upgrade and uninstall the sshd service and a special switch to completely clean up server keys on an uninstall.

## Chocolatey Package Testing and Features
Generally the Chocolatey package is tested for upgrade from the previous release, forced install on top of itself (fix a broken install) and clean uninstall.
For upgrade and forced install scenarios the Chocolatey package leaves existing server keys and config files in place - it requires a special switch to remove these during uninstall.

## Differences Between Chocolatey Package and Install Instructions Above
These differences are mainly due to assuming you are deploying this automatically to a set of servers - not just playing on one.
- Sets sshd service to start automatically and starts it.
- Opens sshd port in windows firewall for sshd service (and removes it's firewall rules on uninstall)
- Does not call install/uninstall helper scripts - but uses it's own code. (this allowed the chocolatey package not to exhibit lsa integration problems in early releases)
- Works around known problem when possible - for example, currently it detects if logging is commented out per the default configuration and forces it off as the default results in logging half a GB per hour on **some systems**
- Gives next steps advice - for instance if you install the sshd server to use key based authentication the package *does not start the sshd service* and advises that you need to reboot to fully enact key based authentication.

## Chocolatey Package Usage Scenarios
Here is the package page: [https://chocolatey.org/packages/win32-openssh]

Here are the individual steps (or use the oneliners below to do all steps):
- Install Chocolate Package Manager using one of the command lines on the Chocolatey.org home page [https://chocolatey.org]
- Install SSH client tools without sshd server (one line): `choco install win32-openssh -confirm`
- Install SSH client tools AND sshd server (one line): `choco install win32-openssh -params '"/SSHServerFeature /KeyBasedAuthenticationFeature"' -confirm`
- Upgrade SSH client tools without sshd server (one line): `choco upgrade win32-openssh -confirm`
- Upgrade SSH client tools AND sshd server (one line): `choco upgrade win32-openssh -params '"/SSHServerFeature"' -confirm`
- Uninstall SSH client tools (including server if present): `choco upgrade win32-openssh -params '"/SSHServerFeature"' -confirm`
- Uninstall SSH client tools, including server if present and remove all generated config including server keys: `choco upgrade win32-openssh -params '"/SSHServerFeature /DeleteConfigAndServerKeys"' -confirm`

## Oneliner To Install Chocolatey and Win32-OpenSSH All At Once
If you would like to install Chocolatey and OpenSSH in one go, drop this oneliner into an elevated PowerShell Prompt:
`[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {[bool]1};set-executionpolicy RemoteSigned -Force -EA 'SilentlyContinue';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/DarwinJS/ChocoPackages/master/win32-openssh/InstallChoco_and_win32-openssh.ps1'))`

To do the same thing, but also install the server, use:
`[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {[bool]1};set-executionpolicy RemoteSigned -Force -EA 'SilentlyContinue';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/DarwinJS/ChocoPackages/master/win32-openssh/InstallChoco_and_win32-openssh_with_server.ps1'))`

