---
title: "Moving Away From Docker Desktop and Running Docker Containers On Minikube"
date: 2021-11-06 20:52:43
tags:
- docker
- docker-desktop
- minikube
- sourcegraph
- volume
- persisted-volume
---

As you may have heard, with the change in Docker licensing Docker-Desktop will require a license for large organizations. As soon as I saw this change, I started looking for alternatives to run docker images on my Mac and after some research I decided to use [minikube](https://minikube.sigs.k8s.io/).

An important aspect to keep in mind is persistence when running containers with minikube. The minikube manages a virtual machine as kubernetes node and the docker containers you run will execute on this virtual machine. So, persistence layer for the containers are also on the virtual machine.

I will give an example to be more clear. One of the containers I like to keep running is Sourcegraph. I like to index my personal code repositories and do a search on all of them. Sourcegraph is very smart with code indexing and helps me find certain tokens on distributed repositories.

If you examine the [documentation from Sourcegraph](https://docs.sourcegraph.com/admin/install/docker), it recommends the following command to run an instance as a container:

```bash
docker run --publish 7080:7080 \
--publish 127.0.0.1:3370:3370 \
--rm \
--volume ~/.sourcegraph/config:/etc/sourcegraph \
--volume ~/.sourcegraph/data:/var/opt/sourcegraph \
sourcegraph/server:3.33.0
```

As I mentioned, there is a problem if we execute this command with minikube because the docker container runs in the virtual machine (VM) when using minikube and only certain folders are persisted from the VM. For details, please see [Minikube-Persistent Volumes](https://minikube.sigs.k8s.io/docs/handbook/persistent_volumes/). Every time the container restarts, you will need to configure the server from scratch or [sync the configuration manually](https://docs.sourcegraph.com/admin/config/advanced_config_file).

So with a minor adjustment to the `docker run` command we can move the container volumes to one of the persisted volumes (i.e. `/data`) on the minikube node.

```bash
docker run --publish 7080:7080 \
--publish 127.0.0.1:3370:3370 \
--restart=always \
--mount type=bind,source=/data/sourcegraph/config,destination=/etc/sourcegraph \
--mount type=bind,source=/data/sourcegraph/data,destination=/var/opt/sourcegraph \
sourcegraph/server:3.33.0
```

After this configuration, Sourcegraph will start automatically after `minikube start` command (note `restart=always`) and it will persist the configuration and indexes (note the `--mount` arguments) unless the minikube node is deleted.

Happy coding!
