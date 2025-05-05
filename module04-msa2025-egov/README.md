# egov msa krds
- Korea Design System https://www.krds.go.kr/
- https://github.com/kenu/egovframe-common-components-msa-krds

## 프로젝트 구동 방법

1. EurekaService 실행
2. ConfigServer
   - `ConfigServer/src/main/resources/config/application-local.yml`
   - DB, Token, role 설정 확인
   - 샘플 DB : ([공통컴포넌트 테이블 정보](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:com:v4.1:init_table#:~:text=%EC%9A%B4%EC%A0%84%EB%A9%B4%ED%97%88%EC%A6%9D%20SP%20%EA%B1%B0%EB%9E%98%EC%A0%95%EB%B3%B4-,%ED%85%8C%EC%9D%B4%EB%B8%94/%EC%B4%88%EA%B8%B0%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%83%9D%EC%84%B1%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8,-%EA%B3%B5%ED%86%B5%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%8A%94%20%EB%B0%B0%ED%8F%AC%ED%8C%8C%EC%9D%BC%EC%9D%84%20%ED%86%B5%ED%95%B4))
3. GatewayServer 실행 (기본 포트 :9000)
4. EgovMain 실행 (메인 레이아웃)
5. EgovLogin 실행 (로그인 후 인증토큰이 있어야 컴포넌트 사용 가능)
6. 필요한 컴포넌트 실행

- LoginPolicy
- Author
- Board
- Questionnaire
- Cmmncode

## AWS EC2
- Spot Instance
- 16G t3a.xlarge

### env setup
- zsh, git, docker, java17, maven
- https://okdevtv.com/mib/egov/msa

```sh
sudo dnf install zsh git util-linux-user htop maven docker -y

sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

```

```sh
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git

```

```
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)

```

```sh
sudo usermod -a -G docker ec2-user

DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins

```

```sh
curl -SL https://github.com/docker/compose/releases/download/v2.35.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker compose version

```

```sh
sudo usermod -a -G docker ec2-user
```

### Code download
```
take ~/git
pwd
git clone https://github.com/kenu/egovframe-common-components-msa-krds
cd egovframe-common-components-msa-krds
git checkout -b kenu origin/kenu
```

### Database
```sh
cd docker-compose/mysql
pwd
docker compose up -d
docker ps
```

### Build
```
cd ~/git/egovframe-common-components-msa-krds
mvn clean package
```

### Run
```sh
~/git/egovframe-common-components-msa-krds
cd EurekaServer
nohup java -jar target/EurekaServer.jar&
cd ../ConfigServer
nohup java -jar target/ConfigServer.jar&
cd ../GatewayServer
nohup java -jar target/GatewayServer.jar&
cd ../EgovMain
nohup java -jar target/EgovMain.jar&
cd ../EgovLogin
nohup java -jar target/EgovLogin.jar&
cd ../EgovLoginPolicy
nohup java -jar target/EgovLoginPolicy.jar&
cd ../EgovAuthor
nohup java -jar target/EgovAuthor.jar&
cd ../EgovBoard
nohup java -jar target/EgovBoard.jar&
cd ../Questionnaire
nohup java -jar target/Questionnaire.jar&

```

### Account

| 구분    | ID         | PW       | 비고        |
| ----- | ---------- | -------- | --------- |
| 업무사용자 | TEST1      | rhdxhd12 | 영문으로 공통12 |
|       | webmaster  | rhdxhd12 | 영문으로 공통12 |
| 일반사용자 | USER       | rhdxhd12 | 영문으로 공통12 |
| 기업사용자 | ENTERPRISE | rhdxhd12 | 영문으로 공통12 |
