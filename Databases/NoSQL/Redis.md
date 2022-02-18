# Redis pentesting
## Overview
**Redis' Security Posture**

1. Securing a Redis database via a password is optional
2. No built-in TLS

Has feature called "Protected mode"

This comes with Redis 3.2.0 and whenever Redis is executed with the default configurations it enters a special mode called *protected mode.* While in this mode, Redis only replies to queries from the loopback interface.

According to Redis' Documentation (https://redis.io/topics/security) - It's possible to disable commands in Redis or to rename them into an unquessable name, so that normalt clients are limited to a specified set of commands.

``````
CONFIG GET       # Gives the current set of configuration
CONFIG SET       # Sets the configuration of the default command
Example -> Config SET dir /var/www

# File enumeration has occured in the past due to dofile being allowed

eval "dofile('/var/www')" 0 # Directory Exists but can't open file
eval "dofile('/var/test')" 0 # No such directory exists

``````
### Default pots used by redis
```ports
6379
16379
26379
```