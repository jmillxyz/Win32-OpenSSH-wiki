**For this build we used 64 bit Debian Linux.**

`# uname -a`

`Linux teledebian 3.2.0-4-amd64 #1 SMP Debian 3.2.54-2 x86_64 GNU/Linux`

**The build-essential package will install all the necessary tools for compilation**

`# apt-get install build-essential`

**Lets make a dir win32openssh**

`# mkdir win32openssh`

`# cd win32openssh`

**Download zlib**

`# wget http://zlib.net/zlib-1.2.8.tar.gz`

**Unzip zlib**

`# tar -zxvf zlib-1.2.8.tar.gz`

**Build and Install Zlib**

`# cd zlib-1.2.8 `

`# ./configure `

`# make `

`# make install `

`# cd ..`

**Download OpenSSL**

`# wget https://www.openssl.org/source/openssl-1.0.2d.tar.gz`

**Unzip OpenSSL**

`# tar -zxvf openssl-1.0.2d.tar.gz`

**Build and Install OpenSSL**

`# cd openssl-1.0.2d `

`# ./config ` 
                      
`# make `

`# make test `

`# make install`

`# cd ..`

**Download Win32-OpenSSH**

`# wget https://github.com/PowerShell/Win32-OpenSSH/archive/L1.zip`

**Unzip Win32-OpenSSH**

`# unzip L1.zip`

**Build Win32-OpenSSH**

`# cd Win32-OpenSSH-L1 `

`# chmod 744 configure`

`# ./configure --with-ipv4-default --with-md5-passwords`

`# make`
