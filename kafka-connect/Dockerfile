FROM openjdk:8-jre

RUN apt-get update && apt-get install -y procps
RUN apt-get install net-tools
RUN apt-get install jq -y
RUN apt-get install vim -y

WORKDIR /opt/kafka-connect/confluent

COPY resources/jars/mysql-connector-java-5.1.47.jar /opt/kafka-connect/confluent/confluent-5.1.2/share/java/kafka-connect-jdbc/
COPY resources/jars/splunk-kafka-connect-v1.1.0.jar /opt/kafka-connect/confluent/jars/
COPY resources/jars/kafka-connect.jar /opt/kafka-connect/confluent/jars/
COPY resources/connectors-config/kafka-connect-file-sink.json /opt/kafka-connect/confluent/config/
COPY resources/connectors-config/kafka-connect-jdbc-source.json /opt/kafka-connect/confluent/config/
COPY resources/connectors-config/connect-distributed.properties /opt/kafka-connect/confluent/config/
COPY resources/start-kafka-connect.sh /opt/kafka-connect/confluent/

RUN chmod 700 /opt/kafka-connect/confluent/start-kafka-connect.sh

CMD ["./start-kafka-connect.sh"]