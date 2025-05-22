# egov msa service discovery

### 서비스 디스커버리 구현
1. 디스커버리 서비스 (Eureka 서버):
  - `/backend/discovery` 경로에 위치
  - Spring Cloud Netflix Eureka Server (`@EnableEurekaServer` 어노테이션 사용)
  - `application.yml`에 다음과 같이 구성:
    - 포트: 8761
    - 기본 인증 (사용자명/비밀번호: admin/admin)
    - `register-with-eureka: false`로 자기 자신의 등록 비활성화
2. 마이크로서비스 (Eureka 클라이언트):
  - 모든 마이크로서비스는 `@EnableDiscoveryClient` 어노테이션 사용
  - Eureka 클라이언트 의존성 포함: `spring-cloud-starter-netflix-eureka-client`
  - 서비스 목록:
    - apigateway
    - user-service
    - board-service
    - portal-service
    - reserve-item-service
    - reserve-check-service
    - reserve-request-service
3. API 게이트웨이 통합:
  - 동적 라우팅을 위해 서비스 디스커버리 사용
  - 라우트는 `lb://` 접두사(로드 밸런서) 다음에 서비스 이름으로 구성
  - 예: `uri: lb://USER-SERVICE`
  - 디스커버리 로케이터 활성화: `discovery.locator.enabled: true`
4. 쿠버네티스 배포:
  - 디스커버리 서비스는 `discovery-deployment`로 배포
  - 8761 포트로 노출
  - actuator를 통한 헬스 체크 구성
5. Docker Compose 구성:
  - 디스커버리 서비스 컨테이너 이름: discovery
  - 다른 서비스들은 `EUREKA_INSTANCE_HOSTNAME: discovery`로 구성

서비스 디스커버리 설정을 통해 마이크로서비스들은 Eureka 서버에 자신을 등록하고 서비스 위치를 하드코딩하지 않고도 다른 서비스를 동적으로 발견할 수 있어, 더 탄력적이고 확장 가능한 아키텍처를 구현할 수 있습니다.
