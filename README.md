#Supported tags and respective Dockerfile links

- [`0.0.4`, `0.0.4` (*0.0.4/Dockerfile*)](https://github.com/Accenture/adop-nexus/blob/master/Dockerfile.md)

# What is adop-logstash?
adop-logstash is a wrapper for the [logstash](https://hub.docker.com/_/logstash/) image. It has primarily been built to perform extended configuration. Logstash is a wrapper for the official Logstash image, this image has primarily been built to include a configuration file for logstash.

# How to use this image?

The easiest for to run adop-logstash image is as follow:
```
docker run --name <your-container-name> -d -p 500:500 -p 12201:12201 -p 25826:25826 adop/logstash:VERSION
```

## Configuration

The Logstash configuration is externalised and stored the 'resources' directory.

# License
Please view [licence information](LICENCE.md) for the software contained on this image.

#Supported Docker versions

This image is officially supported on Docker version 1.9.1.
Support for older versions (down to 1.6) is provided on a best-effort basis.

# User feedback

## Documentation
Documentation for this image is available in the [Logstash documenation page](http://logstash.net/docs/1.3.2//configuration). 
Additional documentaion can be found under the [`docker-library/docs` GitHub repo](https://github.com/docker-library/docs). Be sure to familiarize yourself with the [repository's `README.md` file](https://github.com/docker-library/docs/blob/master/README.md) before attempting a pull request.

## Issues
If you have any problems with or questions about this image, please contact us through a [GitHub issue](https://github.com/Accenture/adop-logstash/issues).

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

Before you start to code, we recommend discussing your plans through a [GitHub issue](https://github.com/Accenture/adop-logstash/issues), especially for more ambitious contributions. This gives other contributors a chance to point you in the right direction, give you feedback on your design, and help you find out if someone else is working on the same thing.
