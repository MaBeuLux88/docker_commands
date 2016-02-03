# Commands docker
A quick reminder of the most commonly used docker commands.

Install docker : `curl -sSL https://get.docker.com/ | sh`

Allow user XYZ to use docker without root access : `sudo usermod -aG docker XYZ`

Version docker : `docker version` 

Start Hello World : `docker run hello-world`

List all the images already downloaded : `docker images`

Pull an image from Docker Hub : `docker pull tomcat` or `docker pull ubuntu:12.04`

Start container and execute command : `docker run ubuntu echo "Hello World"`

Start container with shell interaction : `docker run -it ubuntu bash`

Exit the container without killing it : `CTRL + P + Q together`

See the running containers : `docker ps`

See all the containers : `docker ps -a`

Start container detached : `docker run -d ubuntu ping 127.0.01 -c 50`

Start container detached with exposed ports : `docker run -d -P tomcat`

List all containers short ID : `docker ps -aq`

List short ID of the last container : `docker -lq`

Stop container : `docker stop <ID>`

Kill container : `docker kill <ID>`

Remove container : `docker rm <ID>`

Remove container and its volumes : `docker 'rm -v <ID>`

Start container : `docker start <ID>`

Start container and attach : `docker start -a <ID>`

Stop all containers : `docker ps -aq | xargs docker stop`

Remove all containers : `docker ps -aq | xargs docker rm`

See all containers exited : `docker ps -a --filter "exited=1"`

Add sheel interaction to a detached container : `docker exec -it <ID> bash`

View output from container process PID 1 : `docker logs <ID>`

View logs in live : `docker logs -f <ID>`

View logs 5 last lines in live : `docker --tail 5 -f <ID>`

Get container infos in JSON : `docker inspect <ID>`

Get IP address from JSON : `docker inspect --format='{{.NetworkSettings.IPAddress}}' <ID>`

Get valid JSON out of it : `docker inspect --format='{{json .NetworkSettings}}' <ID>`

Show container differences from last layer : `docker diff <ID>`

Commit the state of a container : `docker commit <ID> mabeulux88/debian_better:1.0`

Build image from Dockerfile : `docker build <path>`

Build image from Dockerfile in the current folder : `docker build .`

Build image from Dockerfile with tag : `docker build -t mabeulux88/myImage:1.0 <path>`

Build image from Dockerfile without cache : `docker build --no-cache .`

Remove image : `docker rmi <ID>`

Show build layer history : `docker history <ID>`

Login to Docker Hub : `docker login`

Push image to Docker Hub : `docker push <repo:tag>` with repo = "mabeulux88/hellojava"

Tag image : `docker tag <ID> repo:tag`

Create container with volume : `docker run -d -it -v /data/www debian`

Get volume path : `docker inspect -f '{{json .Volumes}}' <ID>`

Mount host folders to a volume : `docker run -v [host path]:[container path]:[rw|ro]`

Create data container : `docker run --name datacontainer -v /data/app1 busybox true`

Run container using data container : `docker run -it --volumes-from datacontainer ubuntu`

Backup container : `docker run --volumes-from logdata -v /home/toto/backups:/backups ubuntu tar cvzf /backup/my_logs.tar /var/log/app`

Map port 8080 tomcat container to 80 on host : `docker run -d -p 80:8080 tomcat` 

Execute command in container : `docker exec <ID> my_command`

Show the ports mapped : `docker port <ID>`



# Example Dockerfile

## ping localhost

```
#Super comment and super image
FROM debian
RUN apt-get update
RUN apt-get install -y wget vim figlet
CMD ping localhost -c 20
CMD ["ping", "localhost", "-c", "20"]
ENTRYPOINT ["figlet"]
```

## ping localhost by default but can accept "google.fr" as a parameter.

```
#Super comment and super image
FROM debian
RUN apt-get update
RUN apt-get install -y wget vim 
RUN apt-get install -y figlet
ENTRYPOINT ["ping", "-c", "5"]
CMD ["localhost"]
```

Run like this : 
```
docker build -t ping:1.0 .
docker run ping:1.0
docker run ping:1.0 google.fr
```

## Compile and run Java Application

```
FROM java
# The "/" at the end of /home/java/src/ is mandatory
COPY Hello.java /home/java/src/
WORKDIR /home/java
RUN mkdir bin
RUN javac -d bin src/Hello.java
ENTRYPOINT ["java", "-cp", "bin", "Hello"]
```

Run like this : 
```
docker build -t hellojava .
docker run hellojava
// to check it's ok inside the container
docker run -it --entrypoint bash hellojava
```

# Fix Installation Docker Debian Stretch (testing)

By default, when docker is installed on a Debian Stretch with `curl -sSL https://get.docker.com/ | sh`, 
it runs on the Storage Driver "Devicemapper" which is utterly slow!

You can check this in the `docker info`. 

My problem was that Devicemapper is utterly slow : 

For example, this command was taking me 6-7 seconds :
```
time docker run -it --rm debian echo "Hello World"
```

Here is how to fix it by switching to your storage engine to "overlay" but it's going to wiped all the images / containers you have.
```
sudo service docker stop
sudo rm -rf /var/lib/docker
sudo mkdir -p /etc/systemd/system/docker.service.d/
sudo vim /etc/systemd/system/docker.service.d/override.conf

copy/paste this then save and exit : 
[Service]
ExecStart=
ExecStart=/usr/bin/docker daemon -s overlay -H fd:// --userland-proxy=false --exec-opt native.cgroupdriver=cgroupfs

sudo systemctl daemon-reload
sudo service docker start
```

Now the `docker info` command should display something like this :

```
Containers: 0
Images: 4
Server Version: 1.9.1
Storage Driver: overlay
 Backing Filesystem: extfs
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 4.2.0-1-amd64
Operating System: Debian GNU/Linux stretch/sid (containerized)
CPUs: 4
Total Memory: 7.678 GiB
Name: zenikaMax
ID: WH3X:DUHT:5J62:LX6E:DT24:5TL4:W75D:7H6M:P3PQ:7323:RMI7:H2BK
Username: mabeulux88
Registry: https://index.docker.io/v1/
WARNING: No memory limit support
WARNING: No swap limit support
```

Now my Storage Driver is using the "overlay" and my initial command takes less than a second to run!!!

Hope this help :-) !
Thank you @vdemeester !

