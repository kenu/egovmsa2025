## RabbitMQ
### 1\. RabbitMQ란?
RabbitMQ는 AMQP(Advanced Message Queuing Protocol)를 구현한 오픈소스 메시지 브로커이다.
애플리케이션(Producer)이 보낸 메시지를 **브로커(Broker)**가 받아 Queue에 저장한 뒤, 다른 애플리케이션(Consumer)이 읽어 갈 때까지 안전하게 보관-전달해준다. 

| 구분| 설명| 패턴별 활용 예시|
|-|-|-|
| **Broker**| 메시지를 받아 저장·전달하는 **RabbitMQ 서버**| 모든 패턴에서 **중앙 허브** 역할|
| **Exchange**| Producer가 보낸 메시지를 **어느 Queue로 라우팅**할지 결정 | • **Work-Queue** → 기본(anonymous) Exchange, **하나의 Queue**로 직접 전달<br>• **Fanout Pub/Sub** → Fanout Exchange가 **모든 구독 Queue**로 복사<br>• **Routing** → Direct/Topic Exchange가 **라우팅 키별 Queue** 선택 |
| **Queue**| 메시지를 **버퍼링(FIFO)** 하는 저장소| • Work-Queue → **1 개 Queue + 다수 Worker**<br>• Pub/Sub → 구독자 수만큼 **각각의 Queue**<br>• RPC → **요청 Queue + 응답 Queue** 두 개|
| **Producer**| 메시지를 **발행 (publish)** 하는 애플리케이션| 패턴과 무관하게 **발신자**|
| **Consumer**| Queue에서 메시지를 **소비 (consume)**| 패턴과 무관하게 **수신자**|
| **Binding / Routing Key** | **Exchange ↔ Queue** 연결 규칙·라벨| • Fanout → Binding Key 무시(모두 전달)<br>• Direct → **라우팅 키 = 바인딩 키**<br>• Topic → 와일드카드 `*.error` 등 패턴 매칭|

### 2\. Exchange 타입

| Exchange 타입 | 별명·패턴      | 라우팅 키 사용? | 동작                                                      | 대표 용도                       |
| ----------- | ---------- | --------- | ------------------------------------------------------- | --------------------------- |
| **fanout**  | **브로드캐스트** | ✖︎ (무시)   | 받은 메시지를 **연결된 모든 큐**로 복사                                | 시스템 공지, 로그 전파               |
| **direct**  | **단순 라우팅** | ✔︎(완전 일치) | `routingKey` 값과 **바인딩 키가 100 % 일치**하는 큐 1 개(또는 다수)에만 전송 | Work Queue, 레벨별 로그(`error`) |
| **topic**   | **패턴 라우팅** | ✔︎(와일드카드) | `a.b.*`, `#.kr` 같은 **패턴**에 맞춘 큐로 전송                     | 국가·서비스별 이벤트 분기              |
| **headers** | 헤더 기반      | ✔︎(헤더)    | AMQP 헤더 값에 따라 전송                                        | 복잡한 메타데이터 라우팅               |


## 메시징 아키텍처 패턴별 구조도

### 1. Work Queue (작업 대기열)

[![Rabbitmq For Queue Management In Laravel – peerdh.com](https://tse4.mm.bing.net/th?id=OIP.z3It7YZzFxTN4aCsHjo2SwHaDt\&pid=Api)](https://peerdh.com/es/blogs/programming-insights/rabbitmq-for-queue-management-in-laravel)

“줄 서서 하나씩 꺼내가는 공장 컨베이어”

>한 큐에 작업을 몰아 두고 여러 Worker가 “1 개씩” 꺼내 가면 자동으로 부하가 분산된다

|   단계  | 무슨 일이 일어나나?                                              |
| :---: | -------------------------------------------------------- |
| **①** | Producer가 “일감” 메시지를 **단일 Queue**(버퍼)에 적재                 |
| **②** | 브로커가 라운드로빈(혹은 prefetch = 1) 방식으로 **여러 Worker**에게 1 건씩 전달 |
| **③** | Worker가 처리 완료 후 ACK → 다음 메시지 배분                          |
| **④** | Worker가 죽어 ACK를 못 보내면, 브로커가 동일 메시지를 **다른 Worker**에게 재전달  |

| 특징        | 설명                                         |
| --------- | ------------------------------------------ |
| **목적**    | 무거운 작업을 백그라운드 워커로 분산 ▶ 웹 응답 지연 최소화         |
| **흐름**    | Producer → **하나의 큐** → 다수 Worker(라운드로빈 분배) |
| **적합 사례** | 이메일 전송, 이미지/영상 처리, PDF 생성 등                |

---

### 2. Publish / Subscribe (브로드캐스트 방식)

[![GCP pub/sub Architecture Understanding | by Kamal Maiti | Medium](https://tse2.mm.bing.net/th?id=OIP.9WkNrdQKZ2mIGrQBed7vbAHaEK\&r=0\&pid=Api)](https://medium.com/%40kamal.maiti/gcp-pub-sub-architecture-understanding-199e1f7913b7)

>하나의 이벤트를 여러 서비스가 동시에 들어야 할 때, 브로커가 이벤트를 복사해 각자의 큐에 뿌려 준다.,<br>
“주문 생성” → 알림 시스템·재고 시스템·로깅 시스템이 동시에 반응

|   단계  | 무슨 일이 일어나나?                                     |
| :---: | ----------------------------------------------- |
| **①** | Producer가 **Fanout / Topic Exchange**에 이벤트 발행   |
| **②** | Exchange가 연결된 **모든 큐**(혹은 패턴 매칭된 큐)에 **복사본** 전달 |
| **③** | 각 Consumer가 자기 큐에서 독립적으로 메시지 소비                 |
| **④** | 어떤 Consumer 하나가 느려도 다른 큐·서비스엔 영향 X              |


| 특징        | 설명                                             |
| --------- | ---------------------------------------------- |
| **목적**    | **한 이벤트를 여러 소비자**가 동시에 수신                      |
| **흐름**    | Producer → Topic/Fanout Exchange → **각 구독자 큐** |
| **적합 사례** | 알림 시스템, 로그 파이프라인, 이벤트-드리븐 마이크로서비스              |

Fanout Exchange
- 누구에게 보낼지 따로 표식(라우팅 키)을 붙이지 않는다.<br>
그래서 브로드캐스트(전체 전파) 용: “주문 생성!”, “시스템 경보!”처럼 모든 팀이 알아야 할 이벤트.

Topic Exchange
- 편지(메시지)에 주소 라벨 error.payment, user.signup.kr 같은 토픽 키를 붙여 보냄.
<br>
우체국(Exchange)은 와일드카드 규칙(error.*, user.#)대로 맞는 사서함(큐) 에만 투입.<br>
“결제 오류만 모아 받아”, “한국 사용자 이벤트만 구독” 같은 선별 수신이 가능.

---



### 3. Routing (Direct / Topic)

[![Part 4: RabbitMQ Exchanges, routing keys and bindings - CloudAMQP](https://tse2.mm.bing.net/th?id=OIP.3JAVL6gtcp3LnEhiU4t1wwHaHy\&pid=Api)](https://www.cloudamqp.com/blog/2015-09-03-part4-rabbitmq-for-beginners-exchanges-routing-keys-bindings.html)

>이벤트마다 ‘주소 라벨’(Routing Key) 을 붙여서, 필요한 소비자에게만 배달한다.
<br>로깅 예: DEBUG/INFO 로그는 Dev 큐, ERROR 로그만 Alert 큐에 전달

|   단계  | 무슨 일이 일어나나?                                              |
| :---: | -------------------------------------------------------- |
| **①** | Producer가 `error.payment` 같은 **Routing Key** 포함 메시지 발행   |
| **②** | Exchange가 **바인딩 규칙**(`error.*`)과 비교 → 매치되는 큐에만 전달        |
| **③** | `error.#` 큐: 모든 에러 로그 수집, `error.payment` 큐: 결제 오류만 수집 등 |
| **④** | 필요 없는 서비스는 메시지를 전혀 받지 않아 **오버헤드 최소화**                    |


| 특징        | 설명                                               |
| --------- | ------------------------------------------------ |
| **목적**    | 라우팅 키(텍스트 패턴)로 **메시지를 선별** 전달                    |
| **흐름**    | Producer → Direct/Topic Exchange → 바인딩 키와 일치하는 큐 |
| **적합 사례** | 로그 레벨별 분기(`error.*`), 국가·카테고리별 구독 등              |

---

### 4. RPC (원격 프로시저 호출)

[![PPT - Remote Procedure Calls (RPC) PowerPoint Presentation, free ...](https://tse4.mm.bing.net/th?id=OIP.nqBz7kKwa87S0L-X0Wzi6AHaFj\&pid=Api)](https://www.slideserve.com/miya/remote-procedure-calls-rpc)

>요청 큐 + 응답 큐 두 개를 써서, “요청 → 결과 반환” 패턴을 메시징으로 만든다.<br>
HTTP REST 호출 못 쓰는 폐쇄망·IoT 환경에서도 “질문/답변” 패턴 구현



| 특징        | 설명                                                                      |
| --------- | ----------------------------------------------------------------------- |
| **목적**    | 요청-응답 호출을 **메시지 큐 기반**으로 구현                                             |
| **흐름**    | Client → **요청 큐** (correlation ID 포함)<br>Server → 처리 후 **응답 큐** 로 결과 전송 |
| **적합 사례** | 결제 승인, 재고 조회 등 결과 값이 필요한 서비스-간 통신                                       |

---


### 6\.정리 및 비교
| 패턴                       | 핵심 아이디어                                  | 1줄 흐름                                    |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Work-Queue**           | 작업을 한 큐에 쌓고 워커 N개가 1개씩 꺼내 처리 → **부하 분산** | Producer → Queue → Worker×N              |
| **Fanout Pub/Sub**       | 이벤트 1건을 구독자 수만큼 **복사**해 전달               | Producer → Fanout Ex → 모든 Queue          |
| **Direct/Topic Routing** | **라우팅 키** 기준으로 원하는 큐에만 배달                | Producer → Direct/Topic Ex → 매칭 Queue    |
| **RPC**                  | 요청 Queue + 응답 Queue로 **질문/답변** 구현        | Client → Req Queue ⇄ Resp Queue ← Server |

> **TIP — 패턴 선택 가이드**
>
> * **Work Queue** : “처리량 분산·지연 허용” 작업
> * **Pub/Sub** : “동일 이벤트, 다중 후속 액션”
> * **Routing** : “메시지를 조건별로 선별”
> * **RPC** : “비동기 방식으로도 즉답이 꼭 필요”