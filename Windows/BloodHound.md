# BloodHound

## Installation

### Python Version
``BloodHound.py``

https://github.com/fox-it/BloodHound.py

BloodHound.py is a Python based ingestor for BloodHound, based on Impacket.

This version of BloodHound.py is only compatible with BloodHound 3.0 or newer.
````bash
python3 -m pip install bloodhound
````

### Normal version
NEEDTOBEDONE!!

https://github.com/BloodHoundAD/BloodHound

## Usage
### Start neo4j databse
````bash
neo4j start | restart | stop
````
**Default User + Password is neo4j:neo4j**
If you use HTB pwnbox, then there is no password else go to /etc/neo4j/neo4j.conf and uncomment security.auth
### BloodHound-python

````bash
bloodhound-python -d <domain> -u <Username> -p <Password> -gc <Forest_Domain> -c all -ns <IP-ADDRESS>
````

