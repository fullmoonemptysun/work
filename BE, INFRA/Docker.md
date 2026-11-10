
### 3 parts to the interface:
1. **The "Docker server" or "Docker Daemon".** This listens to requests from the desktop app and executes them. If this isn't running nothing else will work.
2. **The "Docker Desktop" GUI.** Starting the GUI should start the server, at least that's how I usually ensure the server is running. The GUI is the visual way to interact with Docker.
3. **The Docker CLI.** As a developer, most of your work will be interacting with Docker via the CLI. I'd recommend using the GUI to visualize what's going on with Docker, but executing most of your commands through the command line.

### What even is docker?
>*"it works on my machine..."*
>*"then we will ship it with your machine"*

Docker allows us to deploy our applications inside "containers", which are kind of like _very_ lightweight virtual machines. Instead of just shipping an application, we can ship an application _and the environment it runs in_.

## VMS vs Containers
- Containers abstract at the OS level and not at the hardware level. Far more less resource intensive than VMs.
- If the goal is to only run processes in an environment, for the most part, containers provide the same isolation with much less resources.
- The architecture makes the difference:
	- VMs : Host OS | Hypervisor | VM|Guest OS|Apps (each vm has its own OS)
	- Containers: Host OS | Docker | Apps (all instances share the same OS).

It almost looks like all the containers have their own dedicated resources, but they're actually being shared in a secure way using **namespaces**.


## Docker Image vs Containers
- Image is a read only definition of a container meanwhile container is a running instance of the image that can be modified. 
`docker run imgname`
we can pull an image from docker hub like:
`docker pull imgname`   instead of downloading it.


### How to start a container
`docker run -d -p hostport:containerport namespace/name:tag`
- `-d`: Run in detached mode (doesn't block your terminal)
- `-p`: Publish a container's port to the host (forwarding)
- `hostport`: The port on your local machine
- `containerport`: The port inside the container
- `namespace/name`: The name of the image (usually in the format `username/repo`)
- `tag`: The version of the image (often `latest`)

`docker run -d -p docker/getting-started:latest` 

`docker ps` lists all the processes running (containers)

`docker ps -a` lists all the processes that may or may not be running. 


### How to stop a container
2 ways:
`docker stop conatiner_id` : issues a SIGTERM to container (use this mainly)
`docker kill container_id` : issues a SIGKILL to container (use only as last resort)


It is normal to have an entire fleet of docker containers running. The hostport for each should be different (multiple processes cannot bind to the same port).


when we have `hostport:containerport` basically we are saying the process inside the container is running on containerport and all the data from/to it will be forwarded to hostport.

### Docker volumes
By default, a container is volatile. So once it closes, all the data it wrote, all the state is lost. And when a new container is spun from the image, it starts at the original state of the image not with the data changes made by the previous container.

To fix this, there are docker volumes. We create one by doing 
`docker volume create volume_name`
`docker volume ls` lists all the documents
`docker volume inspect volume_name` shows info about where the volume really lives on the disk

Side note: **Ghost** is blogging service like WordPress, and docker has a ready image for it on their hub.

### Using volumes

```sh
    docker run -d \
      -e NODE_ENV=development \
      -e url=http://localhost:3001 \
      -e database__connection__filename=/var/lib/ghost/content/data/ghost-dev.db \
      -p 3001:2368 \
      -v ghost-vol:/var/lib/ghost/content \
      ghost
```


- `-d` runs the image in detached mode to avoid blocking the terminal.
- `-e NODE_ENV=development` sets an [environment variable](https://en.wikipedia.org/wiki/Environment_variable) within the container. This tells Ghost to run in "development" mode (rather than "production", for instance)
- `-e url=http://localhost:3001` sets another environment variable, this one tells Ghost that we want to be able to access Ghost via a URL on our host machine.
- `-e database__connection__filename=/var/lib/ghost/content/data/ghost-dev.db` specifies where Ghost stores its SQLite database.
- We've used `-p` before. `-p 3001:2368` does some [port-forwarding](https://en.wikipedia.org/wiki/Port_forwarding) between the container and our host machine.
- `-v ghost-vol:/var/lib/ghost/content` mounts the volume we created at Ghost's content directory, so its files, including the database, survive when we replace the container. If this volume does not exist, it will create a new one from scratch. 

>[!error] Warning
>**Docker Desktop on Linux quirk:** Docker Desktop runs the daemon inside a VM, so it doesn’t use `/var/run/docker.sock` or `docker.service`; its socket is per-user at `~/.docker/desktop/docker.sock`, which means you must run `docker` as your normal user (not root) with Docker Desktop started, and `docker context use desktop-linux` if needed. Because of the VM, the `Mountpoint` shown by `docker volume inspect` (like `/var/lib/docker/volumes/x/_data`) is a path inside the VM, not on your real filesystem, and the actual data lives in one big disk image (`~/.docker/desktop/vms/0/data/Docker.raw`). To see or copy a volume’s files, use a throwaway container (`docker run --rm -v my_volume:/data alpine ls /data`) or the Volumes tab in the GUI, and if you want files directly visible on the host, use a bind mount to a real folder instead of a named volume.


#### Difference between the two port configs
- The hostport:container port is port forwarding forwarding requests to the host port.
- Meanwhile the url variable is to let Ghost know the url people are using to access it and so if it generates any links it will be accordingly. The public url.




### Exec 
Usually, we run a container and just let the process run do its thing. But if we need to run individual commands we need to run inside a container, we do it by:
`docker exec CONTAINER_ID command`


Being able to run one-off commands is nice, but it's often more convenient to start a shell session running within the container itself. Thats where the `-i` and `-t` flags come in:

- `-i` makes the `exec` command interactive
- `-t` gives us a [tty (keyboard) interface](https://en.wikipedia.org/wiki/Tty_\(Unix\))
- Running `/bin/sh` gives us a shell session inside the container

For example:

```sh
docker exec -it CONTAINER_ID /bin/sh
```


### Offline
To cut off and isolate a container from any external network connections, we use the `--network none`  flag with `docker run`. 


