# Docker
Platform & Version: Docker for Windows, Docker 1.17 unless otherwise noted. Some commands require a bash compatible shell.

## Setup
Docker for Windows 10 finally should improve on the [VPN](https://github.com/boot2docker/boot2docker/issues/628) [issues](http://blog.frickjack.com/2017/01/vpn-blocking-docker-routes-on-windows.html),
 as it does not rely on the Virtualbox interfaces anymore. 
However new issues seem to arise with Wi-Fi. On first setup, I landed [here](https://github.com/docker/for-win/issues/139). 
Fortunately the Docker doc has some [troubleshooting help](https://docs.docker.com/docker-for-windows/troubleshoot/#hyper-v) on the topic. 
Best part of all that? It does not seem to be Dockers fault. After playing around with the virtual switches of Hyper-V, 
just adding even an external one, seems to kill the Wi-Fi interface. So what has Microsoft to say? A [little](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/setup-nat-network) [here](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/01/09/using-hyper-v-with-a-wireless-network-adapter/) and [there](https://blogs.msdn.microsoft.com/virtual_pc_guy/2016/05/02/windows-10-hyper-v-and-wireless-a-new-way-to-make-this-all-work/) .


## Container management
Find the IP address of a running container
```shell
 docker inspect <container> -f "{{ .NetworkSettings.IPAddress }}"
```
Filtering inspect inspect is achieved with [GO templating](https://golang.org/pkg/text/template/).
```shell
# Combine with static text
docker inspect storage -f "The container {{.Config.Hostname}}{{.Name}} has IP {{ .NetworkSettings.IPAddress }}"

# Or techy for all containers for later reuse
docker inspect $(docker ps|awk '{print $1}'|grep -iv container) -f "{{.Config.Hostname}}={{ .NetworkSettings.IPAddress }}"
f445927fa494=172.17.0.2

# A little cleaner
docker ps -q | xargs docker inspect -f "{{.Config.Hostname}}={{ .NetworkSettings.IPAddress }}"
f445927fa494=172.17.0.2
``` 

Cleanup got a whole lot easier since 1.13! Instead of the piped dangling=true calls one can now:
```
docker system prune
docker container prune
docker image prune
docker volume prune
docker network prune
```

For reference, pre 1.13 this would have looked like
```
# Remove dead containers
docker ps -f status=dead --format '{{ .ID }}' | xargs -r docker rm -v
# Remove dangling volumes
docker volume ls -qf dangling=true | xargs -r docker volume rm
# Remove dangling images
docker images -qf dangling=true | xargs -r docker rmi
```

## Dockerfile

## Docker Machine

