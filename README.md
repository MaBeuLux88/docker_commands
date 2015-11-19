# Commands docker
A quick reminder of the most commonly used docker commands.

Install docker : `curl -sSL https://get.docker.com/ | sh`
Version docker : `docker version` 
Start container detached with exposed ports : `docker run -d -P tomcat`
Stop all containers : `docker ps -aq | xargs docker stop`
Remove all containers : `docker ps -aq | xargs docker rm`
