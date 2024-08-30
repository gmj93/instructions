# Install (Ubuntu, many versions)
```
curl https://get.docker.com | bash
```

# List Docker CLI commands
```
docker
docker container --help
```

# Display Docker version and info
```
docker --version
docker version
docker info
```

# Execute Docker image
```
docker run hello-world
```

# List Docker images
```
docker image ls
docker images -a
```

# List Docker containers (running, all, all in quiet mode)
```
docker container ls
docker container ls --all
docker container ls -aq
```

# Remove images
```
docker rmi IMAGE_ID

docker builder prune
```

# Delete all containers
```
docker rm $(docker ps -a -q)
```

# Delete all images
```
docker rmi $(docker images -q)
```

# Run image
```
docker run image_name
```

# Delete dangling images
```
docker rmi $(docker images -f "dangling=true" -q)
```

# Create container from dockerfile in current folder
```
docker build --tag=<tag name> .
```

# Log into running container
```
docker exec -it <container name> /bin/bash

docker run -it --entrypoint /bin/bash <image name>
```

# Stop running container

```
docker stop <container name>
```

# Run as normal user

```
sudo groupadd docker && sudo usermod -aG docker $USER && newgrp docker
```

# Allow access to host wireless card

```
docker run -it --net="host" --privileged <IMAGE NAME> /bin/bash
```

# Problems when running along with VPN

Create `/etc/openvpn/fix-routes.sh` with:

```
#!/bin/sh

echo "Adding default route to $route_vpn_gateway with /0 mask..."
ip route add default via $route_vpn_gateway

echo "Removing /1 routes..."
ip route del 0.0.0.0/1 via $route_vpn_gateway
ip route del 128.0.0.0/1 via $route_vpn_gateway
```

```
chmod o+x /etc/openvpn/fix-routes.sh
chown root:root  /etc/openvpn/fix-routes.sh
```

Add do VPN config the following lines:

```
script-security 2
route-up  /etc/openvpn/fix-routes.sh
```

# Enable IPv6

These instructions are to enable IPv6 between containers. To enable external access is another process.

Edit or create `/etc/docker/daemon.json` and add the following lines:

```
{
	"ipv6": true,
	"fixed-cidr-v6": "fe81::/80"
}
```

(the fixed-cidr entry can be a real prefix if you have ipv6. In my case I had to make one up that did not clash with the existing address in my interface)

When running the container use `--sysctl net.ipv6.conf.all.disable_ipv6=0` on `docker run` and set the IPs manually later. Use `--privileged`

# Set running container to restart automatically
```
docker update --restart unless-stopped <container>
```

# Move docker data directory to another folder
```
sudo service docker stop
```

Create docker daemon configuration `/etc/docker/daemon.json`
```
{ 
   "data-root": "/path/to/your/new/docker/root"
}
```

```
sudo rsync -aP /var/lib/docker/ "/path/to/your/new/docker/root"
```

```
sudo mv /var/lib/docker /var/lib/docker.old
```

```
sudo service docker start
```

```
rm -rf /var/lib/docker.old
```
