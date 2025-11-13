# Deploying Elasticsearch on an Amazon EC2 instance using Docker Compose

![ElasticSearch Logo](https://upload.wikimedia.org/wikipedia/commons/f/f4/Elasticsearch_logo.svg)

**Author:** Victor Ajiri
**Date:** November 13, 2025
**Tags:** #Elasticsearch #AWS #Docker

---

Elasticsearch is a powerful open-source search and analytics engine used in data indexing, observability, and log management systems. Running it in a containerized environment makes deployment faster, cleaner and more reproducible.
Docker and docker compose simplify deployment and management of applications
Elastic search stores the processed logs and indexes them to allow quick search , kibana allows you create visual dashboards of the logs. Using the Siem app in kibana to monitor logs.

---

This write up walks you through deploying Elasticsearch on an **Amazon EC2** instance using **Docker Compose**.

---

Prerequisites
- Amazon EC2 instance 
- SSH access to instance
- Ports **9200** (HTTP API) and **9300** (Internal communication) open in instance

---

##Connect to EC2 Instance 

-Use SSH to connect :
  ssh -i "key.pem" user@<EC2_Public_IP>
  
---

##Install Docker

-sudo apt update
-sudo apt install -y docker.io

---

##Enable Docker 

-sudo systemctl enable docker
-sudo systemctl start docker

---

##Install Docker Compose

-sudo apt install -y docker-compose

---

##Deploy Elasticsearch

create a file called **'docker-compose.yml'**:

version: "2"

networks:
  elastic:
    driver: bridge

volumes:
  elasticsearch:
    driver: local

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    restart: unless-stopped
    environment:
      - discovery.type=single-node
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
      - xpack.security.enabled=true
      - xpack.security.authc.api_key.enabled=true
      - ELASTIC_PASSWORD=Elastic@123
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - elasticsearch:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - elastic

  ent-search:
    image: docker.elastic.co/enterprise-search/enterprise-search:7.15.0
    restart: unless-stopped
    depends_on:
      - elasticsearch
    environment:
      - JAVA_OPTS=-Xms512m -Xmx512m
      - ENT_SEARCH_DEFAULT_PASSWORD=Elastic@123
      - elasticsearch.username= (**your username**)
      - elasticsearch.password= (**your password**)
      - elasticsearch.host=http://elasticsearch:9200
      - allow_es_settings_modification=true
      - secret_management.encryption_keys=["4a2cd3f81d39bf28738c10db0ca782095ffac07279561809eecc722e0c20eb09"]
      - elasticsearch.startup_retry.interval=15
    ports:
      - "3002:3002"
    networks:
      - elastic

      
---
##Launch it
-docker-compose up -d

---

##Add Kibana For Data Visualization to yml file
-can add to bottom of yml file

 kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    restart: unless-stopped
    depends_on:
      - elasticsearch
      - ent-search
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
      - ENTERPRISESEARCH_HOST=http://ent-search:3002
      - ELASTICSEARCH_USERNAME=(**your username**)
      - ELASTICSEARCH_PASSWORD=(**your password**)
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=X5rtEXBA3Js1sNMu7VpY4QKIEBpjwzkb231
    networks:
      - elastic

---

Access Kibana at 
'http://<EC2_Public_IP>:5601'

---
CONCLUSION


