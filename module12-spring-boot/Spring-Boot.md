# Spring Boot

## 1. 개요: Spring Boot란?

Spring Boot는 스프링 프레임워크 기반의 개발을 간편하고 생산성 높게 만들어주는 프레임워크입니다.  
복잡한 설정 없이 독립 실행형(Spring Boot Application) 애플리케이션을 빠르게 개발할 수 있도록 도와줍니다.

### ✅ 주요 특징
- Convention over Configuration (관례 우선 설정)
- 자동 설정 (Auto Configuration)
- 내장 서버 (Embedded Server) 제공
- 빠른 실행 및 운영 편의성

---

## 2. Spring Boot의 필요성

| 기존 스프링                | Spring Boot                      |
|----------------------------|----------------------------------|
| 복잡한 XML 설정 필요       | 최소 설정, 자동 구성             |
| 별도 WAS 설치 필요         | 내장 Tomcat 실행 가능            |
| 반복적인 설정              | 스타터 의존성으로 간편 구성      |
| 운영 모니터링 미비         | Actuator 제공                    |

### 왜 사용해야 하나요?
- 마이크로서비스 아키텍처에 적합
- 빠른 프로토타이핑/개발 가능
- 클라우드 환경과 친화적

---

## 3. 핵심 구성 요소

### 3.1 @SpringBootApplication 어노테이션

- `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`을 포함한 복합 어노테이션

```java
@SpringBootApplication
public class MyApp {
  public static void main(String[] args) {
    SpringApplication.run(MyApp.class, args);
  }
}
```

### 3.2 자동 설정 (Auto Configuration)
- 클래스패스와 의존성 기반으로 Bean 자동 등록
- 필요시 `application.yml`이나 `@Configuration`으로 커스터마이징 가능

### 3.3 스타터(Starter) 의존성
- 기능별 의존성 모음

```gradle
dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-web'
  implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

### 3.4 내장 서버 (Embedded Server)
- Tomcat, Jetty, Undertow 등을 내장
- 별도 WAS 없이 `java -jar`로 실행 가능

### 3.5 Spring Boot Actuator
- 헬스 체크, 메트릭 수집, 환경 정보 노출 등 운영 기능 자동 제공

| 엔드포인트           | 설명                       |
|----------------------|----------------------------|
| /actuator/health     | 애플리케이션 상태 확인     |
| /actuator/metrics    | 메트릭 데이터 확인         |
| /actuator/env        | 환경 변수 확인             |

---

## 4. 동작 흐름 요약

```
[개발자] 
    ↓ @SpringBootApplication
[SpringApplication.run()] 
    ↓ 자동 설정 / 내장 서버 시작
[요청 처리] 
    ↓ @RestController, Service, Repository 작동
[운영 관리]
    ↓ Actuator 통해 상태 확인 및 모니터링
```

---

## 5. 장단점

### ✅ 장점
- 설정 최소화, 빠른 시작
- 내장 WAS로 배포 간소화
- 운영 모니터링 기능 내장
- 마이크로서비스 환경에 적합
- 다양한 스타터와 에코시스템 지원

### ❌ 단점
- 자동 설정으로 인해 불필요한 Bean/의존성 포함 가능성
- 복잡한 설정을 직접 제어하기 어려운 경우 있음
- 초기에 내부 동작 원리를 이해하지 않으면 디버깅이 어려움

---

## 6. 실습 예시

### 6.1 간단한 REST API 예시

```java
@RestController
public class HelloController {

  @GetMapping("/hello")
  public String hello() {
    return "Hello, Spring Boot!";
  }
}
```

### 6.2 application.yml 설정 예시

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

---

## 7. 확장: Spring Boot의 활용 분야

| 분야                | 적용 예                                 |
|---------------------|-----------------------------------------|
| REST API 서버       | 사용자 인증, 게시판, 상품 관리 등        |
| 백엔드 마이크로서비스 | 주문 서비스, 결제 서비스 등             |
| 배치 애플리케이션   | 정기 데이터 수집/처리                   |
| 모니터링 도구       | Actuator 활용 서버 상태 점검            |
| 클라우드 배포       | AWS, Naver Cloud, Heroku 등과 연동 용이 |

---

## 8. 참고 자료

- 📘 [Spring Boot 공식 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- 📚 [Baeldung - Spring Boot Guide](https://www.baeldung.com/spring-boot)
- 🎓 [MSA School - Spring Boot](https://www.msaschool.io/operation/introduction/)