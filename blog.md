# Deploying Elasticsearch on an Amazon EC2 instance using Docker Compose

![ElasticSearch Logo](https://upload.wikimedia.org/wikipedia/commons/f/f4/Elasticsearch_logo.svg)

**Author:** Victor Ajiri
**Date:** November 13, 2025
**Tags:** #Elasticsearch #AWS #Docker

---

Elasticsearch is a powerful open-source search and analytics engine used in data indexing, observability, and log management systems. Running it in a containerized environment makes deployment faster, cleaner and more reproducible.

---

This write up walks you through deploying Elasticsearch on an **Amazon EC2** instance using **Docker Compose**.

---

Prerequisites
- Amazon EC2 instance 
- SSH access to instance
- Ports **9200** (HTTP API) and **9300** (Internal communication) open in instance


##Connect to EC2 Instance 
-Use SSH to connect :
  ssh -i "key.pem" user@<EC2_Public_IP>

##Install Docker
-sudo apt update
-sudo apt install -y docker.io

##Enable Docker 
-sudo systemctl enable docker
-sudo systemctl start docker

##Install Docker Compose
-sudo apt install -y docker-compose


