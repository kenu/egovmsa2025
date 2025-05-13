#  DDD(도메인 드리븐 디자인) 의 역사

----

## 1. DDD의 탄생 (2000년대 초반)

### Eric Evans와 Blue Book (2003)

- **2003년**: Eric Evans가 "Domain-Driven Design: Tackling Complexity in the Heart of Software" 출간
- [PDF](https://fabiofumarola.github.io/nosql/readingMaterial/Evans03.pdf)
- 이 책은 흔히 "Blue Book"으로 불리며 DDD의 바이블로 여겨짐
- Evans는 수년간의 컨설팅 경험을 바탕으로 복잡한 소프트웨어 시스템 개발의 문제를 해결하고자 함
- 소프트웨어가 비즈니스 도메인을 정확히 모델링해야 한다는 혁신적 아이디어 제시

### DDD 이전의 문제들
- 기술 중심의 개발 방식
- 도메인 전문가와 개발자 간의 소통 단절
- 복잡한 비즈니스 로직이 코드 전반에 흩어짐
- 지속적인 유지보수의 어려움

## 2. DDD의 개념 형성 과정 (1990년대 후반)

### 선행 사상들

- **1990년대**: Extreme Programming(XP), Agile 방법론이 등장하면서 개발자와 비즈니스 전문가의 협업 강조
- **Object-Oriented Programming**: 객체지향 프로그래밍의 성숙과 함께 도메인 모델링 개념 발달
- **Pattern 운동**: GoF 디자인 패턴(1994) 이후 패턴을 통한 복잡성 관리 아이디어 확산
- **실제로 도메인 드리븐 디자인은 XP와 OOP두개의 학파의 영향을  받은 개념입니다.**

### Eric Evans의 경험
- Evans는 1990년대 말 여러 프로젝트에서 컨설팅하며 복잡한 도메인 로직 관리 문제를 직면
- 성공한 프로젝트들의 공통점을 분석하여 DDD 원리 도출

## 3. DDD의 성장과 확산 (2004-2010)
### 초기 적용과 실험
- **2004-2006**: 주로 엔터프라이즈 Java 커뮤니티에서 큰 관심
- Spring Framework, Hibernate 등과 함께 사용되며 실제 프로젝트 적용 증가
- DDD를 적용한 사례 연구 발표 시작

### 대중화와 논의

- **2007-2010**: 다양한 컨퍼런스에서 DDD 세션 증가
- 블로그, 아티클을 통한 경험 공유 활발
- DDD의 일부 개념(예: Repository Pattern)이 널리 채택됨

## 4. DDD의 진화 (2010년대)

### 마이크로서비스와 DDD

- **2012-2015**: 마이크로서비스 아키텍처 패러다임과 DDD의 Bounded Context 개념이 완벽하게 부합
- Netflix, Amazon 등이 마이크로서비스에 DDD 원리 적용
- **Bounded Context가 마이크로서비스의 경계를 정의하는 핵심 개념이 됨**

## 5. DDD의 현재와 미래 (2020년대)

### 현대적 구현과 도구

- **Docker, Kubernetes**: 컨테이너 기술로 Bounded Context의 물리적 분리 용이
- **Event-driven Architecture**: Apache Kafka 등을 통한 Domain Events 구현

### 실무 적용 사례

- **대규모 기업들**: Netflix, Spotify, Airbnb 등이 DDD 원리로 시스템 설계
- **스타트업**: 빠른 성장과 변화에 대응하기 위해 DDD 채택 증가
- **레거시 시스템 리팩토링**: 기존 시스템을 DDD로 재설계하는 프로젝트 증가

### 확장된 개념들

- **Domain-driven Design Distilled** (Vaughn Vernon, 2016): DDD의 핵심 개념을 간결하게 정리
- **Implementing Domain-Driven Design** (Vaughn Vernon, 2013): 실무 구현에 초점
- **DDD Gently** (Avraham Poupko, 2016): 초심자를 위한 가이드

## 6. DDD의 영향과 유산

### 소프트웨어 개발 문화 변화

- 도메인 전문가와 개발자의 협업 강조
- 비즈니스 로직을 중심으로 한 설계 사고
- 복잡성 관리에 대한 체계적 접근
## 7. 주요 인물들

### Eric Evans
- DDD의 창시자
- 계속해서 DDD 커뮤니티 리더 역할 수행

<img src="./images/eric-evans.png" alt="Eric Evans">

### Vaughn Vernon
- "Implementing Domain-Driven Design"의 저자
- Reactive 프로그래밍과 DDD 결합 선도
- 액터 모델과 DDD 통합 연구

<img src="./images/vaughn-vernon.png" alt="Vaughn Vernon">

### Martin Fowler
- Blue Book에 서문 작성
- Enterprise Architecture Patterns와 DDD 연결
- DDD 개념을 더 넓은 커뮤니티에 소개

<img src="./images/martin-fowler.png" alt="Martin Fowler">

### Greg Young
- CQRS 패턴 제안
- Event Sourcing 개념 정립
- DDD의 Event 관련 패턴 발전에 기여

<img src="./images/greg-young.png" alt="Greg Young">

## 참고
- image from: [google](https://www.google.com/search?newwindow=1&sca_esv=38312a7202a7ceff&sxsrf=AHTn8zpUhITWAZmTQDoARTSynSH59dx_Mw:1747099680920&q=Eric+Evans&stick=H4sIAAAAAAAAAONgFuLRT9c3NEouNC3MLUtTQuFpCQZnpqSWJ1YW-6VWlASXpBYU_2IUC0jNL8hJVUjMKc5XKE5NLErOUEjLL1rEyuValJms4FqWmFe8g5XxFpskQ5hf-OclNWvcOTVCUlIdIza5cRf7_FTiPAoA5232oHYAAAA&sa=X&ved=2ahUKEwjVjPm-pZ-NAxXA3zQHHTt_A-AQzO0BKAR6BAgbEBM&biw=1920&bih=888&dpr=1)