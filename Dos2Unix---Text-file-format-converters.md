The EOL (End Of Line) character in DOS is '\r\n'

The EOL (End Of Line) character in UNIX is '\n'

Editing UNIX files on Windows changes the EOL from UNIX to DOS, which might make the UNIX build tools not working. Also this make file comparison difficult. Use the following steps to batch convert files from DOS to UNIX EOL:

Using PowerShell
```powershell
Get-ChildItem -File -Recurse | % { $x = get-content -raw -path $_.fullname; $x -replace "`r`n","`n" | set-content -path $_.fullname }
```

or

* Download _**dos2unix.exe**_ program from [http://sourceforge.net/projects/dos2unix/?source=typ_redirect](http://sourceforge.net/projects/dos2unix/?source=typ_redirect) 
* Uncompress the compressed file (lets say, dos2unix-7.3-win32.zip)
* Put the bin directory of the uncompressed file (that has dos2unix.exe and other programs) in system path
* Open a command shell and go to the directory where you want to convert files and run


_// to convert all files_

`for /R %G in (*.*) do dos2unix "%G"`

_// to convert all .h files_

`for /R %G in (*.h) do dos2unix "%G"`

_// to convert all .c file_

`for /R %G in (*.c) do dos2unix "%G"`