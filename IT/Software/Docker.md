`docker build -t <image_name>` - build an image from a Dockerfile

`docker images` - list local images

`docker rmi <image_name>` - delete an image

`docker search <image_name>` - search Docker Hub for an image

`docker pull <image_name>` - pull an image from a Docker Hub

`docker run --name <container_name> <iamge_name>` - create and run a container from an image, with a custom name

`docker run -p <host_port>:<container_port> <image_name>` - run a container with and publish a container's port(s) to the host

`docker run -d <image_name>` - run a container in the background

`docker start|stop <container_name>` - start or stop an existing container

`docker rm <container_name>` - remove a stopped container

`docker exec -it <container_name> sh` - open a shell inside a running container

`docker ps` - list currently running containers

`docker ps --all` - list all docker containers (running and stopped)

`docker container stats` - view resource usage stats
