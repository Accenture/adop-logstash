# Docker logstash repo

This is the Git repo for the Accenture DevOps Platform logstash docker image.

# What is Logstash?

Logstash is a wrapper for the official Logstash image, this image has primarily been built to include a configuration file for logstash.

# How to use this image

	docker run --name <your-container-name> -d -p 500:500 -p 12201:12201 -p 25826:25826 docker.accenture.com/adop/docker-logstash:VERSION

## Configuration

The Logstash configuration is externalised and stored the 'resources' directory.