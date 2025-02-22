### Kafka options
***
Any options given to Maxwell that are prefixed with `kafka.` will be passed directly into the Kafka producer configuration
(with `kafka.` stripped off).  We use the "new producer" configuration, as described here:
[http://kafka.apache.org/documentation.html#newproducerconfigs](http://kafka.apache.org/documentation.html#newproducerconfigs)

Maxwell sets the following Kafka options by default, but you can override them in `config.properties`.

- kafka.acks = 1
- kafka.compression.type = gzip

Maxwell writes to a kafka topic named "maxwell" by default.  This can be changed with the `kafka_topic` option.

### Kafka key
***
Maxwell generates keys for its Kafka messages based upon a mysql row's primary key in JSON format:

```
{ "database":"test_tb","table":"test_tbl","pk.id":4,"pk.part2":"hello"}
```

This key is designed to co-operate with Kafka's log compaction, which will save the last-known
value for a key, allowing Maxwell's Kafka stream to retain the last-known value for a row and act
as a source of truth.

### Partitioning
***
A binlog event's partition is chosen by the following:

```
  DATABASE_NAME.hash() % TOPIC.NUMBER_OF_PARTITIONS
```

Maxwell will discover the number of partitions in its kafka topic upon boot.  This means that you should pre-create your kafka topics,
and with at least as many partitions as you have logical databases:

```
bin/kafka-topics.sh --zookeeper ZK_HOST:2181 --create \
                    --topic maxwell --partitions 20 --replication-factor 2
```


[http://kafka.apache.org/documentation.html#quickstart](http://kafka.apache.org/documentation.html#quickstart)


<script>
  jQuery(document).ready(function () {
    jQuery("table").addClass("table table-condensed table-bordered table-hover");
  });
</script>
