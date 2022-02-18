<img src="https://raw.githubusercontent.com/rezasp/Trash/master/joomscan.png" width="200"><img src="https://raw.githubusercontent.com/rezasp/Trash/master/owasp.png" width="350">

======

OWASP JoomScan Project
---------------

OWASP Joomla! Vulnerability Scanner (JoomScan) is an open source project, developed with the aim of automating the task of vulnerability detection and reliability assurance in Joomla CMS deployments. Implemented in Perl, this tool enables seamless and effortless scanning of Joomla installations, while leaving a minimal footprint with its lightweight and modular architecture. It not only detects known offensive vulnerabilities, but also is able to detect many misconfigurations and admin-level shortcomings that can be exploited by adversaries to compromise the system. Furthermore, OWASP JoomScan provides a user-friendly interface and compiles the final reports in both text and HTML formats for ease of use and minimization of reporting overheads.
<br>
OWASP JoomScan is included in Kali Linux distributions.

# INSTALL
``````bash
    git clone https://github.com/rezasp/joomscan.git
    cd joomscan
    perl joomscan.pl
	
## For Docker installation and usage

    # Build the docker image
    docker build -t rezasp/joomscan .

    # Run a new docker container with reports directory mounted at the host
    docker run -it -v /path/to/reports:/home/joomscan/reports --name joomscan_cli rezasp/joomscan

    # For accessing the docker container you can run the following command
    docker run -it -v /path/to/reports:/home/joomscan/reports --name joomscan_cli --entrypoint /bin/bash rezasp/joomscan
``````
# JOOMSCAN ARGUMENTS
``````
    Usage:	joomscan.pl [options]

    --url | -u <URL>                |   The Joomla URL/domain to scan.
    --enumerate-components | -ec    |   Try to enumerate components.

    --cookie <String>               |   Set cookie.
    --user-agent | -a <user-agent>  |   Use the specified User-Agent.
    --random-agent | -r             |   Use a random User-Agent.
    --timeout <time-out>            |   set timeout.
    --about                         |   About Author
    --update                        |   Update to the latest version.
    --help | -h                     |   This help screen.
    --version                       |   Output the current version and exit.
``````

# OWASP JOOMSCAN USAGE EXAMPLES

Do default checks...<br>
```bash
perl joomscan.pl --url www.example.com
```

```bash
perl joomscan.pl -u www.example.com
```
<br>
<br>
Enumerate installed components...<br>
```perl joomscan.pl --url www.example.com --enumerate-components```<br>
or<br>
```perl joomscan.pl -u www.example.com --ec```<br>
<br>

Set cookie<br>
```perl joomscan.pl --url www.example.com --cookie "test=demo;"```
<br><br>

Set user-agent<br>
```perl joomscan.pl --url www.example.com --user-agent "Googlebot/2.1 (+http://www.googlebot.com/bot.html)"```<br>
or<br>
```perl joomscan.pl -u www.example.com -a "Googlebot/2.1 (+http://www.googlebot.com/bot.html)"```<br>

Set random user-agent<br>
```perl joomscan.pl -u www.example.com --random-agent```<br>
or<br>
```perl joomscan.pl --url www.example.com -r```<br>

Set proxy<br>
```perl joomscan.pl --url www.example.com --proxy http://127.0.0.1:8080```<br>
or<br>
```perl joomscan.pl -u www.example.com --proxy https://127.0.0.1:443```<br>

Update Joomscan...<br>
```perl joomscan.pl --update```<br><br>


# OWASP PAGE

[https://www.owasp.org/index.php/Category:OWASP_Joomla_Vulnerability_Scanner_Project](https://www.owasp.org/index.php/Category:OWASP_Joomla_Vulnerability_Scanner_Project)

# GIT REPOSITORY

[https://github.com/rezasp/joomscan](https://github.com/rezasp/joomscan)

OWASP JoomScan introduction (Youtube)

[![OWASP JoomScan introduction](https://img.youtube.com/vi/Ik2CJ9LkuoI/0.jpg)](https://www.youtube.com/watch?v=Ik2CJ9LkuoI)