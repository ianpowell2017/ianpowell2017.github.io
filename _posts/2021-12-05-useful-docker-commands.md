---
title: Useful Docker commands
author: ianpowell
date: 2021-12-05 14:10:00 +0000
categories: [docker]
tags: []
---

- Docker Version - `docker -v`
- List Docker images - `docker image list`
- Download an image - `docker pull nginx:1.13.8`
- Remove docker image - `docker rmi {image id}`
- To run an image and then interact with the command line - `docker run -it nginx:1.13.8 /bin/bash`
- To execute commands within an already running container - `docker exec -it containername /bin/bash`
- List containers running - `docker container list`
- List containers running or stopped - `docker container list -a`
- To stop a container - `docker stop {container id}`
- To remove a container - `docker rm {container id}`

To copy files into a container
`docker cp /tmp/config.ini grafana:/usr/share/grafana/conf/`
`docker cp /tmp/config.ini 1477326feb62:/usr/share/grafana/conf/`


