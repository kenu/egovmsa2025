MSA ec2

- https://github.com/egovframework/egovframe-msa-edu

#Requirements
- RAM 16GB 이상 (t3a.2xlarge)

###실행 순서
- config(8888)
- discovery(8761)
- apigateway(8000)              : rabbitmq 서버 필요
- board-service(0)           port: 0 random port
- user-service(0)
- portal-service(0)
- reserve-check-service(0)
- reserve-item-service(0)
- reserve-request-service(0)

- [[dependency]]

---
## basic ec2 install

- ec2 libraries
```sh
sudo dnf install zsh git util-linux-user htop maven docker -y

sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

- oh-my-zsh plugin
```sh
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

- `vi ~/.zshrc`
- `/(gi`
- `dd`

- oh-my-zsh plugin setting
```sh
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

- nvm node 14 install
```sh
node
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.2/install.sh | bash
. ~/.zshrc
nvm i 14
```

- docker permission
```sh
sudo usermod -a -G docker ec2-user

DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
```

- docker compose
```sh
curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker compose version

```

`sudo reboot`

## msa sample

- Clone project for deploy
```sh
docker network create egov-network
take ~/workspace.edu
git clone https://github.com/egovframework/egovframe-msa-edu
cd egovframe-msa-edu/docker-compose/mysql
docker compose up -d
```

- docker inside 1x1
```sh
docker exec -it mysql bash
mysql -u msaportal -p
msaportal
show databases;
exit;
ctrl + D
```

- 최초
```sh
docker run -d -e TZ=Asia/Seoul --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:management
docker run -d -e TZ=Asia/Seoul --name zipkin -p 9411:9411 openzipkin/zipkin
```

- 재실행
```sh
docker run -d -e TZ=Asia/Seoul -p 5672:5672 -p 15672:15672 rabbitmq:management
docker run -d -e TZ=Asia/Seoul -p 9411:9411 openzipkin/zipkin
```

- aws 보안 그룹 15672, 9411, 8000, 3000, 4000 추가
- http://ipaddress:15672 rabbitmq
- http://ipaddress:9411 zipkin
- http://ipaddress:8000 apigateway swagger
- http://ipaddress:3000 admin




- `cd ~/workspace.edu/egovframe-msa-edu/backend/config`
- `vi ./src/main/resources/application.yml`

```sh
      server:
        native:
          search-locations: ${search.location:file:///${user.home}/workspace.edu/egovframe-msa-edu/config} Windows
           search-locations: file://${user.home}/workspace.edu/egovframe-msa-edu/config MacOS
```


```sh
./gradlew build -x test
sleep 3
nohup java -jar build/libs/config-1.0.0.jar&
```


```sh
cd ../discovery
./gradlew build -x test
cd ../apigateway
./gradlew build -x test
cd ../board-service
./gradlew build -x test
cd ../user-service
./gradlew build -x test
cd ../portal-service
./gradlew build -x test

```

```sh
cd ../discovery
nohup java -jar build/libs/discovery-1.0.0.jar&
cd ../apigateway
nohup java -jar build/libs/apigateway-1.0.0.jar&
cd ../board-service
nohup java -jar build/libs/board-service-1.0.0.jar&
cd ../user-service
nohup java -jar build/libs/user-service-1.0.0.jar&
cd ../portal-service
nohup java -jar build/libs/portal-service-1.0.0.jar&

```

```sh
cd ../../frontend/admin
```

- `vi next.config.js`

- `:%s/localhost/IPADDRESS/g`

```sh
npm i
npm run build
npm run start

```

- http://ipaddress:3000
- `1@gmail.com` / `test1234!`

