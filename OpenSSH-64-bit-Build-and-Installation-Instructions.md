#### Build 32-bit openssl, zlib and openssh following 32-bit instructions.  OpenSSH tools are always 32-bit.

#### STEP 1:  Prepare the Cygwin 64 bit environment

##### 1. Download the Cygwin 64 bit installer from  www.cygwin.com

##### 2. Launch the Cygwin installer, and ensure that packages listed below are selected as  'install':

   devel/mingw-*

   devel/mingw64-*

   perl/*

   devel/make: GNU Tool

   devel/autoconf

   devel/autoconf-2.69-2
   
   See REFERENCE VERSIONS below for the detailed list of packages used for reference build.

#### STEP 2: Compile

##### 1. Ensure that you are using correct mingw64 toolchain. You must have administrative rights.
   
   To do that you must create symbolic links:

   run [openssh_dir]/scripts/set-mingw64.sh from the Cygwin /bin directory.

##### 2. Prepare the 64-bit libssl.a and libcrypto.a libraries and the openssl headers. These libraries are used by 64-bit ssh-lsa.

   - Move clean OpenSSL sources into another directory, e.g. openssl-64.

   - Compile sources by running:

     $./Configure mingw64

     $make

##### 3. Build 64-bit ssh-lsa for native RSA/DSA key authorization

  - Move to [openssh_directory]/contrib/win32/win32compat/lsa directory and run:

    $export LIBSSL_PATH="OPENSSL_DIR"

    $make -f Makefile.mingw64

     where OPENSSL_DIR is a directory where openssl sources are extracted
    
  - This command should produce 64-bit ssh-lsa.dll file.

#### STEP 3 - Install ssh-lsa on system where sshd server is running

- Copy the ssh-lsa.dll to the %WINDIR%/System32 directory.
 
IMPORTANT NOTE:

If your Windows is at 64-bit, be sure that you use a 64-bit file manager to copy ssh-lsa.dll, otherwise this dll will be not visible on the 64-bit OS.

For example:

- Drag and drop file using Windows explorer.

Or:

-  Run copy ssh-lsa.dll c:/windows/system32 under a cmd.exe console. 

- Then, by using the regedit tool, add 'ssh-lsa' string to the end of the registry key below:

    HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/Lsa/Authentication Packages
 

Reboot the machine.
 

#### REFERENCE VERSIONS 

##### CYGWIN PACKAGES

13-1           Devel/autoconf: Wrapper for autoconf command

2.13-12        Devel/autoconf2.1: Stable version of the automatic configure builder

2.69-2         Devel/autoconf2.5: An extensible package of m4 macros shell scripts
                                  to automatically configure software code packages

2.23.51-1      Devel/binutils: The GNU assembler, linker and binary utilites

4.8.2-1        Devel/libgcc1: GCC C runtime library

4.8.2-1        Devel/libssp0: GCC Stack-smashing Protection runtime library

4.8.2-1        Devel/libstdc++6: GCC C++ runtime library

4.0-2          Devel/make: The GNU version of 'make' utility

2.23.1-1       Devel/mingw-binutils: Bintutils for MinGW.org win32 toolchain (util)

4.7.3-1        Devel/mingw-gcc-core

4.7.3-1        Devel/mingw-gcc-g++

4.7.3-1        Devel/mingw-gcc-obj
               
20110507-2     Devel/mingw-pthreads: Libpthread for MinGW.org

4.0-1          Devel/mingw-runtime: MinGW.org MSVC & compiler runtime header and libraries

4.0-1          Devel/mingw-w32api

2.22.52-1      Devel/mingw64-i686-binutils

4.7.3-1        Devel/mingw64-i686-gcc-core

4.7.3-1        Devel/mingw64-i686-gcc-g++

3.0.0-1        Devel/mingw64-i686-headers

20100619-5     Devel/mingw64-i686-pthreads

3.0.0-1        Devel/mingw64-i686-runtime

3.0b_svn5935-1 Devel/mingw64-winpthreads

2.22.52-1      Devel/mingw64-x86_64-binutils

4.7.3-1        Devel/mingw64-x86_64-gcc

4.7.3-1        Devel/mingw64-x86_64-core

4.7.3-1        Devel/mingw64-x86_64-g++

3.0.0-1        Devel/mingw64-x86_64-headers

20100619-5     Devel/mingw64-x86_64-pthreads

3.0.0-1        Devel/mingw64-x86_64-runtime

3.0b-svn5935-1 Devel/mingw64-x86_64-winpthreads

5.14.2-3       Perl/perl

 
##### OpenSSL

openssl-1.0.1e