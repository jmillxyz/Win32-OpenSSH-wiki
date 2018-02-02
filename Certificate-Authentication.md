#### Setup SSHD server for certificate based user authentication
- Generate CA keys (just like any other keys)
  - `ssh-keygen -t rsa -f ca_userkeys`
- Register above key as trusted CA for sshd. Add following entry in sshd_config
  - `TrustedUserCAKeys ca_userkeys.pub`
  - Path above is relative to sshd binaries directory. Absolute path is recommended to avoid confusion
- Check out [#1055](https://github.com/PowerShell/Win32-OpenSSH/issues/1055) for considerations while working with domain accounts. 

At this point, server can accept any user certificates signed using ca_userkeys and there is no need to explicitly map user keys in authorized_keys

#### Signing user keys
- sign user keys using ssh-keygen
  - `ssh-keygen.exe -s ca_userkeys -I cert_identity -V -1w:+54w5d -n username id_rsa.pub`
  - `username` should match the user to be authenticated




