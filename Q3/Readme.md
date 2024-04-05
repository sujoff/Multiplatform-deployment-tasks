Challenge 3: Set up elk in docker and send logs from another container. Those logs should appear in Kibana.

---

ELK Stack with Java App Logging
This repo demonstrates how to set up the ELK (Elasticsearch, Logstash, Kibana) stack using Docker and configure a Java application to send logs to it.

Usage
Start ELK stack
Start the ELK docker containers:

bash


Copy code
docker network create elk

docker run -d --name elasticsearch --net elk -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.9.2

docker run -d --name logstash --net elk -p 5000:5000 \\
-v /path/to/logstash.conf:/usr/share/logstash/pipeline/logstash.conf \\  
logstash:7.9.2

docker run -d --name kibana --net elk -p 5601:5601 kibana:7.9.2
Configure Java app
Update the Java application to send logs to Logstash.

For example, using Log4j appender:

java


Copy code
log4j.appender.logstash=org.apache.log4j.net.SocketAppender
log4j.appender.logstash.Port=5000
View logs
Access Kibana at http://localhost:5601 to visualize the logs from the Java app.

Configure index pattern for logstash-* to see the logs.

Architecture
Show Image

The ELK stack containers are networked together and Java app sends logs to Logstash which sends them to Elasticsearch to be indexed and visualized in Kibana.
