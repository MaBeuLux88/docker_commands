# Commands docker
A quick reminder of the most commonly used docker commands.

Install docker : `curl -sSL https://get.docker.com/ | sh`

Allow user XYZ to use docker without root access : `sudo usermod -aG docker XYZ`

Version docker : `docker version` 

Start Hello World : `docker run hello-world`

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


