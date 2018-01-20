Following issues/limitations need to be considered while evaluating Win32 OpenSSH
- [Elevation of Privilege over loopback](https://github.com/PowerShell/Win32-OpenSSH/issues/680) on misconfigured machines.
- Text resources (config and key files) need to be either ASCII or UTF-8 (UTF-16 variants are not supported)
