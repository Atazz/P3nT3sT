# PowerShell

## Bypass execution policy
```powershell
powerShell.exe -ExecutionPolicy Bypass -File .runme.ps1
powerShell.exe -ExecutionPolicy UnRestricted -File .runme.ps1
Get-Content .runme.ps1 | Invoke-Expression
$command = "Write-Host 'My voice is my passport, verify me.'" $bytes = [System.Text.Encoding]::Unicode.GetBytes($command) $encodedCommand = [Convert]::ToBase64String($bytes) powershell.exe -EncodedCommand $encodedCommand

```

## Base64 encoder

[Raiku base64 encoding](https://raikia.com/tool-powershell-encoder/)

This will encode the command you input into valid PowerShell Base64 for use with “EncodedCommand”.

Note: This is not a normal base64 encoder!  It converts the string to UTF-16LE first before encoding, as that is what PowerShell expects!
