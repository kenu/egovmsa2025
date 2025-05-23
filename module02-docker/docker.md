# Docker

- http://docker.com
- Build, Ship, Run
- 개발자와 시스템어드민을 위한 분산 애플리케이션용 오픈 플랫폼

## Install
- https://hub.docker.com/ id 생성
- Docker 설치
  * https://www.docker.com/products/docker-desktop/
  * https://orbstack.dev/ (macOS)

## Basic keywords
```
docker version
docker ps
docker info
docker images
docker container ls
docker container ls -a
```

- `docker run hello-world`
  * docker : 시스템에 있는 docker 사용
  * run : 서브명령, 컨테이너 실행
  * hello-world : 컨테이너에 실을 이미지 이름
- 컨테이너는 아무것도 꾸미지 않은 버전의 리눅스 운영체제
- 고래가라사대
  * docker hub 이미지 정보
    * 포함한 소프트웨어 종류와 사용법
- `docker run -it -p 80:80 docker/getting-started`
- stop

```
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
92d58318f84e        nginx               "nginx -g 'daemon off"   27 hours ago        Up 27 hours         0.0.0.0:80->80/tcp, 443/tcp   webserver
0235bd537f03        nginx               "nginx -g 'daemon off"   27 hours ago        Up 27 hours         80/tcp, 443/tcp               boring_hypatia
➜  ~ docker stop nginx
Error response from daemon: No such container: nginx
➜  ~ docker stop 92d58318f84e
92d58318f84e
```

## 실행중인 도커 접속

```
docker exec -it  92d58318f84e /bin/bash
```

## 이미지 불러오기
- `docker pull imagename`

## 컨테이너 전체 삭제
```
docker ps -aq
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```

## 이미지 만들기
- https://docs.docker.com/get-started/workshop/02_our_app/
- `git clone https://github.com/docker/getting-started-app.git`
- `cd getting-started-app`
- create `Dockerfile`

```dockerfile
# syntax=docker/dockerfile:1

FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

- `docker build -t getting-started .`
- `docker run -p 4000:80 getting-started`
- `curl localhost:4000`
- `docker tag getting-started kenu/get-started:part2`

## 이미지 업로드
- `docker push kenu/get-started:part2`

## 원격 이미지 로컬에서 실행
- `docker run -p 4000:80 kenu/get-started:part2`
- `curl localhost:4000`

## 업로드 이미지 삭제
```
export USERNAME=myuser
export PASSWORD=mypass
export ORGANIZATION=myorg (if it's personal, then it's your username)
export REPOSITORY=myrepo
export TAG=latest

curl -u $USERNAME:$PASSWORD -X "DELETE" https://cloud.docker.com/v2/repositories/$ORGANIZATION/$REPOSITORY/tags/$TAG/
```

## 네트워크
- https://docs.docker.com/engine/tutorials/networkingcontainers/

## AWS EC2
- docker 설치

```
sudo dnf update -y
sudo dnf install docker -y
sudo systemctl start docker
sudo usermod -a -G docker ec2-user
# for ec2-user permission
sudo reboot

docker ps
```

- docker-compose 설치
  * https://docs.docker.com/compose/install/

```sh
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
```

- https://github.com/docker/compose/releases/ # new version
```sh
curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-aarch64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```

```
docker compose version
```

## Cases
- copy file
  * `docker cp d362659da5fc:/opt/app/app.log .`
    * d362659da5fc: container id

## 참고

- Docker 창시자 발표 https://youtu.be/Q5POuMHxW-0

```
docker ps
docker images
docker images ubuntu
docker run -i -t ubuntu:12.10 /bin/bash

ps faxw
ls
rm -rf /var /usr /lib
ls /var
exit

ssh dockerdev
sudo -s
docker ps
docker diff 7b882b11bc8e
docker commit 7b882b11bc8e shykes/broken-ubuntu
docker run -i -t shykes/broken-ubuntu /bin/bash

docker push shykes/broken-ubuntu
https://index.docker.io
```

## related
- [docker compose](/mib/docker/compose)
- [docker mysql](/mib/docker/mysql)
- [docker in Windows](/mib/docker/win)

## ref
- How to remove docker images containers and volumes
  * https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes
- Docker in 12 minutes
  * https://www.youtube.com/watch?v=YFl2mCHdv24
- Docker Compose in 12 minutes
  * https://www.youtube.com/watch?v=Qw9zlE3t8Ko
- Getting Started for non-technical
  * https://docs.docker.com/mac/
  * https://docs.docker.com/docker-for-windows/
- https://docs.docker.com/mac/step_three/
- docker/whalesay
  * https://hub.docker.com/r/docker/whalesay/
- docker for mac
  * https://pilsniak.com/how-to-install-docker-on-mac-os-using-brew/

```
brew install docker docker-compose docker-machine xhyve docker-machine-driver-xhyve
sudo chown root:wheel /usr/local/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chmod u+s /usr/local/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve\nsudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
docker-machine create default --driver xhyve --xhyve-experimental-nfs-share
eval $(docker-machine env default)
docker run hello-world
```
