# Eventuate Platform

![표지](./images/OSSCA-Eventuate%20Platform-01.jpg)
Eventuate Platform은 한 마디로 말하면, 비즈니스 로직에 집중할 수 있도록 분산 데이터 관리를 해결하는 오픈소스 플랫폼입니다.

![목차](./images/OSSCA-Eventuate%20Platform-02.jpg)
Eventuate Platform에 대해 설명하기 전, 전제하는 상황에 대해 설명 후 소개합니다.
이후 Eventuate Platform은 어떤 구조를 가졌는지, 각 구조를 파악해 보겠습니다.
Eventuate Platform에 사용된 Saga와 Event Sourcing에 대해 알아봅니다.
그리고, 관련 문제를 푸는 시간을 갖습니다.

![핵심어로 발표 내용 미리 보기](./images/OSSCA-Eventuate%20Platform-03.jpg)
금일 발표에서는 마이크로 서비스로 리팩토링, [CQRS](../module06-cqrs/cqrs.md), [DDD](../module07-DDD/README.md), , Transaction Messaging에 대한 언급이 조금 있고, SAGA, Event Sourcing, Loose Coupling에 대해 주로 설명할 예정입니다.

![현재 상황 가정](./images/OSSCA-Eventuate%20Platform-04.jpg)
지금 우리는 전자상거래 서비스를 하고 있습니다. 아주 간단하게 하나의 서비스로, 하나의 디비만 가지고 시작했는데, 사업이 갑자기 엄청나게 커져서 마이크로 서비스 아키텍처를 시도해 보자고 합니다.

![마이크로 서비스 아키텍처 도입](./images/OSSCA-Eventuate%20Platform-05.jpg)
서비스를 분리했습니다. 기본적으로 다른 곳에 영향을 받지 않도록 결합을 느슨하게 만들기 위해 각 서비스에 대한 DB도 분리를 했습니다. 그런데, 각 DB의 트랜잭션을 어떻게 관리해야 할지 고민입니다. 이때, 주로 검색되는 것들은 SAGA, CQRS인데, 이걸 언제 다 구현하나 막막합니다.

![Eventuate Platform 소개](./images/OSSCA-Eventuate%20Platform-06.jpg)
Eventuate Platform는 바로 그런 상황을 위해 개발된 오픈 소스 플랫폼입니다. 분산 데이터 관리 문제를 해결해 줍니다. 마이크로 서비스를 위한 설계로 SAGA 패턴을 사용해 데이터의 일관성을 유지해 주고, CQRS 쿼리를 구현했습니다. Transaction Messaging을 이용하고, MySQL 등의 검증된 기술을 이용해 안정성을 높였습니다.

![Eventuate Platform 구조](./images/OSSCA-Eventuate%20Platform-07.jpg)
기본적으로 Tram과 Local 모드로 나뉘어 있습니다. 
CQRS와 SAGA 패턴을 사용해 데이터의 일관성을 유지합니다.
Eventuate Tram과 Eventuate Local은 SAGA 패턴 사용 방식이 다릅니다. 자세히는 아래에서 각 방식을 비교하겠습니다.

![Eventuate Platform Tram 모드와 Local 모드의 비교](./images/OSSCA-Eventuate%20Platform-08.jpg)
Eventuate Local은 이벤트 소싱을 사용해 데이터의 일관성을 유지합니다. Eventuate Tram은 트랜잭션 메시징을 사용해 데이터의 일관성을 유지합니다. 두 가지 모두 SAGA 패턴을 사용해 데이터의 일관성을 유지합니다.


![SAGA 패턴 개념과 동작 설명](./images/OSSCA-Eventuate%20Platform-09.jpg)
SAGA 패턴은 트랜잭션을 여러 개의 로컬 트랜잭션으로 나누어 처리하는 방식입니다. 각 로컬 트랜잭션은 독립적으로 실행되며, 전체 트랜잭션이 성공적으로 완료되면 최종 결과를 반영합니다. 만약 하나의 로컬 트랜잭션이 실패하면, 이전에 성공한 로컬 트랜잭션을 취소하는 보상 트랜잭션을 실행하여 전체 트랜잭션의 일관성을 유지합니다.
계좌 이체를 예로 들면, 송금과 수신을 각각의 로컬 트랜잭션으로 처리하고, 송금이 성공하면 수신을 진행합니다. 만약 송금이 실패하면 수신을 취소하는 보상 트랜잭션을 실행하여 전체 트랜잭션의 일관성을 유지합니다.


![SAGA - Choreography](./images/OSSCA-Eventuate%20Platform-10.jpg)
완전히 탈중앙화된 조율 방식입니다.
마이크로 서비스 아키텍처에서 사가 분산 트랜잭션에 관련된 모든 서비스가 다른 서비스의 이벤트를 구독하여 작업을 수행하는 방식입니다.
이벤트를 발행하는 서비스는 다른 서비스의 상태를 알 필요가 없고, 이벤트를 수신하는 서비스는 이벤트를 발행한 서비스의 상태를 알 필요가 없습니다. 즉, 서로의 상태에 의존하지 않고 독립적으로 동작할 수 있습니다. 이로 인해 시스템의 결합도가 낮아지고, 각 서비스는 독립적으로 배포 및 확장할 수 있습니다.
 모든 서비스는 다른 서비스가 발생시킨 이벤트의 결과로 어떤 작업을 수행할지 정하기 위해 내부적으로 state machine을 유지해야 합니다. 


![SAGA - Orchestration ](./images/OSSCA-Eventuate%20Platform-11.jpg)
중앙 집중식 조율 방식입니다.
하나의 중앙 조정자(coordinator)가 모든 서비스가 올바른 순서로 작업을 실행하도록 조율합니다. 중앙 조정자는 각 서비스의 상태를 모니터링하고, 실패한 경우 보상 트랜잭션을 실행하여 전체 트랜잭션의 일관성을 유지합니다.
복잡한 비즈니스 로직을 처리하는 데 유용하며, 각 서비스가 서로의 상태를 알 필요가 없으므로 결합도가 낮아집니다. 그러나 중앙 조정자가 단일 실패 지점이 될 수 있으므로, 조정자의 장애가 전체 시스템에 영향을 미칠 수 있습니다.


![Event Sourcing - 개념](./images/OSSCA-Eventuate%20Platform-12.jpg)
왼쪽은 전통적인 데이터베이스 모델입니다. 데이터베이스에 상태를 저장하고, 상태를 변경할 때마다 새로운 상태를 덮어씁니다. 오른쪽은 이벤트 소싱 모델입니다. 상태 변경을 나타내는 이벤트를 저장하고, 각 서비스가 해당 이벤트 스토어를 구독하고 있습니다.
이벤트 소싱은 최신 상태를 저장하는 것이 아니라, 상태 변경을 나타내는 이벤트를 저장합니다. 


![Event Sourcing - 동작](./images/OSSCA-Eventuate%20Platform-13.jpg)
현재 상태를 알아야 할 때는 이벤트를 재생하여 현재 상태를 계산합니다. 이벤트는 불변성을 가지므로, 과거의 모든 상태를 추적할 수 있습니다. 이를 통해 시스템의 상태 변경 이력을 쉽게 관리할 수 있습니다.
이벤트 소싱은 병원, 금융 서비스, 전자상거래 등 다양한 분야에서 사용됩니다. 


![핵심어 - 이야기한 내용 확인](./images/OSSCA-Eventuate%20Platform-14.jpg)
이제까지 한 이야기를 정리해 보겠습니다.
마이크로 서비스로 리팩토링할 때 느슨한 결합을 위해 각 서비스에 대한 DB를 분리하는 상황을 가정했습니다. 
이때, Eventuate Platform은 분산 데이터 관리 문제를 해결하기 위해 개발된 오픈 소스 플랫폼입니다.
SAGA 패턴과 CQRS를 사용하여 데이터의 일관성을 유지하고, Eventuate Tram과 Eventuate Local을 통해 트랜잭션 메시징과 이벤트 소싱을 구현합니다. 이를 통해 비즈니스 로직에 집중할 수 있도록 도와줍니다.

![문제 - Event Sourcing](./images/OSSCA-Eventuate%20Platform-15.jpg)
엔티티 변경 이력 등 감사를 위한 기능은 Event Sourcing을 사용하여 구현할 수 있고, Event Platform을 사용하여 구현할 수 있습니다.

![문제 - SAGA](./images/OSSCA-Eventuate%20Platform-16.jpg)
SAGA는 여러 서비스에 걸친 트랜잭션을 관리하는 패턴입니다.
쿼리는 CQRS를 사용하여 구현할 수 있습니다.

![기타 참고](./images/OSSCA-Eventuate%20Platform-17.jpg)
![질문](./images/OSSCA-Eventuate%20Platform-18.jpg)


## 출처
- [Eventuate Platform](https://eventuate.io/)
- [마이크로 서비스 아키텍처 패턴](https://microservices.io/patterns/microservices.html)
- 가상 면접 사례로 배우는 대규모 시스템 설계 기초 2 | 알렉스 쉬 p. 418~419
- [greeks for greeks - Event Sourcing Pattern](https://www.geeksforgeeks.org/event-sourcing-pattern/)


[PDF로 확인하기](./images/OSSCA-Eventuate%20Platform.pdf)
