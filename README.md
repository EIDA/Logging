# Logging
Logging configuration for EIDA services. This stack will ingest and parse the Apache access log on a daily basis. Kibana can be used to visualize the service (dataselect, station, wfcatalog, routing) usage.


## Installation
Install the docker-elk stack: https://github.com/deviantony/docker-elk

  git clone https://github.com/deviantony/docker-elk.git
  
We need to add a mounted volume where ElasticSearch database can store data that has been ingested. In this example we are using `/var/db/elasticsearch` on the host. Make sure this mountpoint has enough free space.

Create the directory on the host and give permisions to 1000:1000 (uid & gid of ElasticSearch inside the container):

    # mkdir /var/db/elasticsearch
    # chown 1000:1000 /var/db/elasticsearch

Inside `docker-compose.yml` under `services.elasticsearch.volumes` add the volume:

    - /var/db/elasticsearch:/usr/share/elasticsearch/data

Change the Logstash pipeline at `./logstash/pipeline/logstash.conf` to the custom EIDA pipeline configuration file `EIDA-pipeline.conf` supplied in this repository:

    cp ~/EIDA-pipeline.conf ./logstash/pipeline/logstash.conf

Build and start the container stack:

    docker-compose up -d
    
* Logstash is available on TCP/5000
* Kibana is available over HTTP on 5601 (interface)
* ElasticSearch is available over HTTP on 9200

You can test the ingestion:

    netcat localhost 5000 < access_log.someday

The index should now appear on Kibana (http://localhost:5601).
