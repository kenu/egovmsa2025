# MSA ec2

- https://github.com/egovframework/egovframe-msa-edu

## Requirements
- RAM 16GB 이상 (t3a.2xlarge)

#### 실행 순서
config(8888)
discovery(8761)
apigateway(8000)              : rabbitmq 서버 필요
board-service(0)           port: 0 # random port
user-service(0)
portal-service(0)
reserve-check-service(0)
reserve-item-service(0)
reserve-request-service(0)

- [[dependency]]

---
## basic install

```
sudo dnf install zsh git util-linux-user htop maven docker -y

sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
```
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

vi ~/.zshrc
/(gi
dd

```
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

```
# node
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.2/install.sh | bash
. ~/.zshrc
# nvm i 14
```

```
sudo usermod -a -G docker ec2-user

DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
```


```
curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker compose version

```



sudo reboot

### msa sample

```
docker network create egov-network
take ~/workspace.edu
git clone https://github.com/egovframework/egovframe-msa-edu
cd egovframe-msa-edu/docker-compose/mysql
docker compose up -d
```

```
docker exec -it mysql bash
mysql -u msaportal -p
msaportal
show databases;
exit;
ctrl + D
```

```
docker run -d -e TZ=Asia/Seoul --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:management
docker run -d -e TZ=Asia/Seoul --name zipkin -p 9411:9411 openzipkin/zipkin
```

```
docker run -d -e TZ=Asia/Seoul -p 5672:5672 -p 15672:15672 rabbitmq:management
docker run -d -e TZ=Asia/Seoul -p 9411:9411 openzipkin/zipkin
```

aws 보안 그룹 15672 추가
http://ipaddress:15672

aws 보안 그룹 9411 추가
http://ipaddress:9411



cd ~/workspace.edu/egovframe-msa-edu/backend/config
vi ./src/main/resources/application.yml
```
      server:
        native:
#           search-locations: ${search.location:file:///${user.home}/workspace.edu/egovframe-msa-edu/config} # Windows
           search-locations: file://${user.home}/workspace.edu/egovframe-msa-edu/config # MacOS
```


```
./gradlew build -x test
sleep 3
nohup java -jar build/libs/config-1.0.0.jar&
# xxx docker build -t config ./
docker run -d -e TZ=Asia/Seoul --name config -p 8888:8888 config
```


```
cd ../discovery
# ./gradlew build -x test
docker build -t discovery .
docker run -d -e TZ=Asia/Seoul --name config -p 8761:8761 discovery

cd ../apigateway
# ./gradlew build -x test
docker build -t apigateway .

cd ../board-service
# ./gradlew build -x test
docker build -t board-service .

cd ../user-service
# ./gradlew build -x test
docker build -t user-service .

cd ../portal-service
# ./gradlew build -x test
docker build -t portal-service .

cd ../reserve-check-service
# ./gradlew build -x test
docker build -t reserve-check-service .

cd ../reserve-item-service
# ./gradlew build -x test
docker build -t reserve-item-service .

cd ../reserve-request-service
# ./gradlew build -x test
docker build -t reserve-request-service .
```

```
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
cd ../reserve-check-service
nohup java -jar build/libs/reserve-check-service-1.0.0.jar&
cd ../reserve-item-service
nohup java -jar build/libs/reserve-item-service-1.0.0.jar&
cd ../reserve-request-service
nohup java -jar build/libs/reserve-request-service-1.0.0.jar&

```


```sh
cd ../../frontend/admin
```

vi next.config.js

```sh
npm i
npm run build
# docker build -t admin .

cd ../portal
cp .env.local.sample .env.local                     
vi .env.local
#4000

vi src/constants/env.ts
# export const PROXY_HOST = process.env.PROXY_HOST || `http://IPADDRESS:${PORT}`
npm i
npm run build
# docker build -t portal .

```

```
docker run -d -e TZ=Asia/Seoul -p 3000:3000 admin
docker run -d -e TZ=Asia/Seoul -p 4000:3000 portal

docker run -d -p 8888:8088 config
docker run -d -p 8761:8088 discovery
docker run -d -p 8000:8088 apigateway
```

`1@gmail.com` / `test1234!`


---
➜  portal git:(contribution) ✗ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           7.9G     0  7.9G   0% /dev/shm
tmpfs           3.2G  8.8M  3.2G   1% /run
/dev/xvda1       30G   11G   20G  35% /
tmpfs           7.9G   36K  7.9G   1% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs           1.6G     0  1.6G   0% /run/user/1000

2024/04/17 09:36 GMT+9
2024/04/17 09:59 GMT+9

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           7.9G     0  7.9G   0% /dev/shm
tmpfs           3.2G  8.9M  3.2G   1% /run
/dev/xvda1       28G  7.1G   21G  26% /
tmpfs           7.9G   32K  7.9G   1% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs           1.6G     0  1.6G   0% /run/user/1000

2024/04/17 10:01 GMT+9
2024/04/17 10:29 GMT+9

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           7.9G     0  7.9G   0% /dev/shm
tmpfs           3.2G  9.2M  3.2G   1% /run
/dev/xvda1       30G  7.6G   23G  26% /
tmpfs           7.9G   32K  7.9G   1% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs           1.6G     0  1.6G   0% /run/user/1000

2024/04/17 11:27 GMT+9
2024/04/17 11:48 GMT+9

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           7.9G     0  7.9G   0% /dev/shm
tmpfs           3.2G  8.8M  3.2G   1% /run
/dev/xvda1       30G  6.3G   24G  21% /
tmpfs           7.9G   64K  7.9G   1% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs           1.6G     0  1.6G   0% /run/user/1000

2024/04/17 13:09 GMT+9
2024/04/17 13:26 GMT+9




---
➜  apigateway git:(contribution) ✗ curl http://localhost:8888
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'reactiveAuthorization': Injection of autowired dependencies failed; nested exception is java.lang.IllegalArgumentException: Could not resolve placeholder 'token.secret' in value "${token.secret}"
Caused by: java.lang.IllegalArgumentException: Could not resolve placeholder 'token.secret' in value "${token.secret}"
