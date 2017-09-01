Following issues/limitations need to be considered while evaluating Win32 OpenSSH
- Global [ssh_config](https://github.com/PowerShell/Win32-OpenSSH/issues/847) and [known_hosts](https://github.com/PowerShell/Win32-OpenSSH/issues/466) are not honored. 
- [Elevation of Privilege over loopback](https://github.com/PowerShell/Win32-OpenSSH/issues/680) on misconfigured machines.
- Text resources (config and key files) need to be either ASCII or UTF-8 (UTF-16 variants are not supported)
