# AMSI bypass

## AMSi.fail
Link to resource (https://amsi.fail/)
AMSI.fail generates obfuscated PowerShell snippets that break or disable AMSI for the current process. The snippets are randomly selected from a small pool of techniques/variations before being obfuscated. Every snippet is obfuscated at runtime/request so that no generated output share the same signatures.

![[Pasted image 20220108215009.png]]


### What is AMSI?
As f-secure explained in one of their excellent blog-posts:

AMSI is an interface on which applications or services (third-party included) are able to scan a script’s content for malicious usage. If a signature in the script is registered by the AMSI antimalware service provider (Windows Defender by default), it will be blocked.
To put this into context, consider the following steps PowerShell takes to integrate with AMSI:

When a PowerShell process is created, AMSI.DLL is loaded from disk into its address space.
Within AMSI.DLL, there’s a function known as AmsiScanBuffer(), essentially the function used to scan a script’s content.
In the PowerShell command prompt, any supplied content would first be sent to AmsiScanBuffer(), before any execution takes place.
Subsequently, AmsiScanBuffer() would check with the registered anti-virus to determine if any signatures have been created.
If the content is deemed malicious, it would be blocked.
Since AMSI relies on being loaded within the process executing the actual script, techniques to break or patch specific functions within amsi.dll are well known.

### Detection
All the demonstrated techniques rely on tampering with the amsi.dll library within the operating process, generic tamper-detection is key.

### Credits
Major credit to MartinIngesen for porting the original C# code into JS! :)
https://github.com/yenchiah/project-website-template
https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell
https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/
https://twitter.com/mattifestation/status/735261120487772160
