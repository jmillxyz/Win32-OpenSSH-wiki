#### Building using Visual Studio 2015

Download and Install VS 2015 [Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)  
Download and Install [Strawberry Perl](http://strawberryperl.com/) (needed for building OpenSSL)  
Download and Install [NASM](http://www.nasm.us)
Open 'VS2015 x64 Native Tools Command Prompt'  
Add Perl and NASM installation directories to %PATH%
Build OpenSSL. (Substitute with your own build dirs where needed)  

    cd D:\programming\OpenSSL\openssl-1.0.2f
    perl Configure debug-VC-WIN64A --prefix=d:\programming\OpenSSL\OpenSSLInstallx64_vs2015-debug
    ms\do_win64a
    nmake -f ms\nt.mak install

    perl Configure VC-WIN64A --prefix=d:\programming\OpenSSL\OpenSSLInstallx64_vs2015
    ms\do_win64a
    nmake -f ms\nt.mak install


Open up "contrib\win32\openssh\paths.targets" in OpenSSH folder.  
Set the paths to point to the right OpenSSL SDK directories Ex:  

    <OpenSSL-Path>D:\programming\OpenSSL\OpenSSLInstallx64_vs2015-debug\</OpenSSL-Path>  
    <OpenSSL-Win32-Release-Path>D:\programming\OpenSSL\OpenSSLInstallx86_vs2015\</OpenSSL-Win32-Release-Path>  
    <OpenSSL-Win32-Debug-Path>D:\programming\OpenSSL\OpenSSLInstallx86_vs2015-debug\</OpenSSL-Win32-Debug-Path>  
    <OpenSSL-x64-Release-Path>D:\programming\OpenSSL\OpenSSLInstallx64_vs2015\</OpenSSL-x64-Release-Path>  
    <OpenSSL-x64-Debug-Path>D:\programming\OpenSSL\OpenSSLInstallx64_vs2015-debug\</OpenSSL-x64-Debug-Path>  


Open "contrib\win32\openssh\Win32-OpenSSH.sln" in Visual Studio and continue building Win32-OpenSSH binaries
