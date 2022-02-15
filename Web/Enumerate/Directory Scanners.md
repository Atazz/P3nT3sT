
# Example of use for dir scanners

## Simple discovery on PHP applications

The main use of these tools is file discovery on a common web server, such as a PHP website running on an apache2. Searching for files on this kind of web server often leads to HTTP errors such as 404 - File not found, 403 - Forbidden or HTTP success such as 200 - OK. Other HTTP status codes may be encountered, like 302 - Found, 429 - Too Many Requests, 500 - Internal Server Error...

Depending on the server configuration, an auditor may or may not include specific HTTP status code during file discovery. The default configuration on most of the tools is to hide 404 - File not found from results. Displayed status codes may vary between tools but 200 - OK is the most common displayed result.

i.e., by default, Dirsearch will print not only 200 status code but also 301, 302, etc.  

```bash
dirsearch -u http://localhost/
dirsearch -u http://localhost/ -e php
dirsearch -u http://localhost/ -e php,php5,sql -w /usr/share/wordlists/raft-large-words.txt -f
```

### Here is other tools doing the same

```bash
dirb http://localhost/ /usr/share/wordlists/raft-large-words.txt -X php,php5,sql
gobuster dir -u http://localhost/ -w /usr/share/wordlists/raft-large-words.txt -x php,php5,sql
ffuf -u http://localhost/FUZZ -w /usr/share/wordlists/raft-large-words.txt -e php,php5,sql
wfuzz --hc 404 -w /usr/share/wordlists/raft-large-words.txt -w exts.txt http://localhost/FUZZFUZ2Z
```



```bash
dirsearch -u http://localhost/ --exclude-texts="Page not found" -e php,php5,sql -w /usr/share/wordlists/raft-large-words.txt -f
ffuf -u http://localhost/FUZZ -fr "Page not found" -w /usr/share/wordlists/raft-large-words.txt -e php,php5,sql
wfuzz --hs "Page not found" --hc 404 -w /usr/share/wordlists/raft-large-words.txt -w exts.txt http://localhost/FUZZFUZ2Z
```

## Webserver with a custom page for error 40X
Sometimes, server won't reply as expected for your tools and will reply a 403 error instead of a 404 error, or worst a 200 status code with a custom error page.

In this case, the auditor must configure his tool to match with the server answer. For the 403 case, the first solution is to exclude 403 results from his tool :

```bash
dirb http://localhost/ /usr/share/wordlists/raft-large-words.txt -X php,php5,sql -N 403
dirsearch -u http://localhost/ -e php,php5,sql -w /usr/share/wordlists/raft-large-words.txt -f -x 403
gobuster dir -u http://localhost/ -w /usr/share/wordlists/raft-large-words.txt -x php,php5,sql -b 403,404
ffuf -u http://localhost/FUZZ -w /usr/share/wordlists/raft-large-words.txt -e php,php5,sql -fc 403
wfuzz --hc 404,403 -w /usr/share/wordlists/raft-large-words.txt -w exts.txt http://localhost/FUZZFUZ2Z
```

i.e., if a website returns a 200 HTTP status code with an HTML page containing the sentence Page not found, you may filter with the following :