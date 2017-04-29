
Issue Management:
 - Check the issues associated with milestone and ensure they are all fixed (fixed issues have a referenced commit). Move unfixed issues to next milestone.
 - Close all fixed issues.

Work to be done in Powershell\openssh-portable:latestw_all:
 - Crank version
   - Upgrade version in appveyor.yml 
   - Upgrade version in .\contrib\win32\openssh\version.rc (following 4 places)
      - FILEVERSION 0,0,12,0
      - PRODUCTVERSION 0,0,12,0
      - VALUE "FileVersion", "0.0.12.0
      - VALUE "ProductVersion", "0.0.12.0"
 - Build Zipped payload
   - Build binaries for x64 and x86 (release)
   - ipmo .\contrib\win32\openssh\OpenSSHBuildHelper.psm1
   - Cross check version of generated bianries - it should reflect the above change in version
   - Package-OpenSSH -NativeHostArch x64 -Configuration Release
   - Package-OpenSSH -NativeHostArch x86 -Configuration Release
   - Zipped payload will be at for ex
     - .\bin\x64\Release\OpenSSH-Win64.zip
     - .\bin\x64\Release\OpenSSH-Win64_Symbols.zip

Sanity testing - Ensure basic ssh remoting scenarios work fine in the following scenarios:
 - On a clean machine 
 - On a nano server node 
 - Basic ssh remote experience into Unix box 

Commit version updates to Powershell\openssh-portable (see prior version update commits for commit message)

Copy payload to Powershell\Win32-OpenSSH:L1-Prod
 - Clone the above repo and copy latest sources from latestw_all to here
 - Push the changes (see prior commits for commit message)

Create a Release
 - Draft a new release on https://github.com/PowerShell/Win32-OpenSSH/releases
 - Set tag with new version (see prior tags)
 - Add release notes similar to prior releases (highlights and pointer to issue list)
 - Include any breaking changes or important notes
 - Upload Zipped payload
 - Update Wiki if needed (installation steps, usage samples, etc)
 - Publish Release
 - Add a pointer to release in milestone and close milestone
