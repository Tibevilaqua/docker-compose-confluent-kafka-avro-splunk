Docker-compose Kafka-connect + Mysql + Splunk integration
======================
```
1 Download confluent here https://www.confluent.io/download-center/
```
```
2 - Copy and paste confluent in the "kafka-connect" folder and extract it
```


Run
======================

Once the containers are running, execute the accessKafkaConnect.sh script then the following commands


```

./confluent-5.1.2/bin/confluent load jdbc_source_mysql_foobar_01 -d connectors-config/kafka-connect-jdbc-source.json
./confluent-5.1.2/bin/confluent unload jdbc_source_mysql_foobar_01

#Verify if the jdbc source config is correct and valid
./confluent-5.1.2/bin/confluent status jdbc_source_mysql_foobar_01

./confluent-5.1.2/bin/confluent load file-sink-mysql-foobar -d connectors-config/kafka-connect-file-sink.json
./confluent-5.1.2/bin/confluent unload file-sink-mysql-foobar


#Verify if the file sink config is correct and valid
./confluent-5.1.2/bin/confluent status file-sink-mysql-foobar



#Start Splunk connect
./confluent-5.1.2/bin/connect-distributed config/connect-distributed.properties


#Verify if it's working
curl http://localhost:9000/connector-plugins


#Add the following configuration to the splunk connector

curl localhost:9000/connectors -X POST -H "Content-Type: application/json" -d'{
  "name": "splunk-prod-financial",
    "config": {
     "connector.class": "com.splunk.kafka.connect.SplunkSinkConnector",
     "tasks.max": "10",
     "topics": "mysql-test",
     "splunk.hec.uri":"http://splunk:8088",
     "splunk.hec.token": "3e6ffd12-0f69-46bb-ad0d-71cffb661a0d",
     "splunk.hec.ack.enabled" : "false",
     "splunk.hec.raw" : "true",
     "splunk.hec.json.event.enrichment" : "org=fin,bu=south-east-us",
     "splunk.hec.track.data" : "true",
     "key.converter": "io.confluent.connect.avro.AvroConverter",
     "key.converter.schemas.enable": "true",
     "key.converter.schema.registry.url": "http://localhost:8081",
     "value.converter": "io.confluent.connect.avro.AvroConverter",
     "value.converter.schema.registry.url": "http://localhost:8081",
     "value.converter.schemas.enable": "true"
    }
}'

# Remove splunk connector config
curl localhost:9000/connectors/splunk-prod-financial -X DELETE

# Verify kafka topic
/opt/kafka-connect/confluent/confluent-5.1.2/bin/kafka-avro-console-consumer --bootstrap-server localhost:9092 \
                              --property schema.registry.url=http://localhost:8081 \
                              --topic mysql-test \
                              --from-beginning --max-messages 1 | \
                              jq '.'



```

Now, execute the accessMySQL.sh script, log into the mysql CLI and insert new values into the test table (password can be found in the docker-compose.yaml file & insert in the mysql/sql folder)



Reference
======================
https://www.confluent.io/blog/simplest-useful-kafka-connect-data-pipeline-world-thereabouts-part-1/

https://www.splunk.com/blog/2018/04/25/splunk-connect-for-kafka-connecting-apache-kafka-with-splunk.html