### Row based replication
***
Maxwell can only operate if row-based replication is on.

```
$ vi my.cnf

[mysqld]
server-id=1
log-bin=master
binlog_format=row
```

Or on a running server:

```
mysql> set global binlog_row_image=FULL;
```

*note*: When changing the binlog format on a running server, currently connected mysql clients will continue to replication in STATEMENT format --
in order to change to row-based replication, you must reconnect all active clients to the server.

### Mysql permissions
***
Maxwell stores all the state it needs within the mysql server itself, in a database called `maxwell`.
```
mysql> GRANT ALL on maxwell.* to 'maxwell'@'%' identified by 'XXXXXX';
mysql> GRANT SELECT, REPLICATION CLIENT, REPLICATION SLAVE on *.* to 'maxwell'@'%';

# or for running maxwell locally:

mysql> GRANT SELECT, REPLICATION CLIENT, REPLICATION SLAVE on *.* to 'maxwell'@'localhost' identified by 'XXXXXX';
mysql> GRANT ALL on maxwell.* to 'maxwell'@'localhost';

```

### Download
***
You'll need a version 7 of a JVM.

```
curl -sLo - https://github.com/zendesk/maxwell/releases/download/v0.17.0/maxwell-0.17.0.tar.gz \
       | tar zxvf -
cd maxwell-0.17.0
```


### STDOUT producer
***
Useful for smoke-testing the thing.

```
bin/maxwell --user='maxwell' --password='XXXXXX' --host='127.0.0.1' --producer=stdout
```


If all goes well you'll see maxwell replaying your inserts:
```
mysql> insert into test.maxwell set id = 5, daemon = 'firebus!  firebus!';
Query OK, 1 row affected (0.04 sec)

(maxwell)
{"table":"maxwell","type":"insert","data":{"id":5,"daemon":"firebus!  firebus!"},"ts": 123456789}
```


### Kafka producer
***
Boot kafka as described here:  [http://kafka.apache.org/07/quickstart.html](http://kafka.apache.org/07/quickstart.html), then:

```
bin/maxwell --user='maxwell' --password='XXXXXX' --host='127.0.0.1' \
   --producer=kafka --kafka.bootstrap.servers=localhost:9092
```

This will start writing to the topic "maxwell".


