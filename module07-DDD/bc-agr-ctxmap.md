
# 바운디드 컨텍스트와  애그리거트


도메인 주도 설계(Domain-Driven Design)에서 바운디드 컨텍스트(Bounded Context), 애그리게이트(Aggregate), 컨텍스트 맵(Context Map)은 복잡한 비즈니스 도메인을 효과적으로 모델링하기 위한 핵심 개념입니다

## 바운디드 컨텍스트 (Bounded Context)

----
바운디드 컨텍스트는 특정 도메인 모델이 적용되는 명확한 경계를 의미합니다.
쉽게 말해, 용어와 규칙이 일관된 의미를 갖는 영역이라고 생각하면 됩니다

### 주요 특징
- **명확한 경계** -  특정 모델이 유효한 범위를 정의합니다.
- **자체 유비쿼터스 언어** - 각 컨텍스트는 고유한 용어와 정의를 가집니다.
- **자율성** - 각 바운디드 컨텍스트는 자체적인 설계 결정과 기술적 구현을 가질 수 있습니다.
- **복잡성 관리** -  큰 시스템을 관리 가능한 부분으로 분할합니다.

### 실제 예시 -  스터디 플랫폼
- **스터디 관리 컨텍스트**
    - 스터디는 제목, 설명, 정원, 모임장, 멤버 목록, 스터디 상태(모집중, 진행중, 완료) 등을 중심으로 정의됩니다
- **등록/신청 컨텍스트**
    - 스터디는 신청 가능한 대상으로, 모집 기간, 필수 자격 요건, 신청서 양식, 승인 프로세스 등의 속성을 가집니다
- **일정 관리 컨텍스트**
    - 스터디는 일련의 모임 일정들의 집합으로, 각 모임 날짜, 시간, 장소, 출석 정보 등이 중요합니다
- **학습 자료 컨텍스트**
    - 스터디는 콘텐츠 저장소로, 자료 목록, 과제, 학습 진도, 공유 문서 등의 측면이 강조됩니다


## 애그리거트 (Aggregate)

----
애그리거트는 하나의 단위로 취급되는 관련 객체들의 클러스터입니다. 애그리거트는 데이터 일관성을 유지하기 위한 경계를 정의합니다

### 주요 특징
- **애그리거트 루트**: 모든 애그리거트는 하나의 루트 엔티티(애그리거트 루트)를 가지며, 외부에서는 이 루트를 통해서만 애그리거트에 접근할 수 있습니다.
- **트랜잭션 일관성**: 애그리거트는 트랜잭션 일관성의 경계를 정의합니다.
- **불변식(Invariant) 보호**: 비즈니스 규칙을 보장하는 불변식을 유지합니다.
- **식별자**: 각 애그리거트 루트는 고유한 식별자를 가집니다.

### 실제 예시
스터디(Study) 애그리게이트를 생각해보겠습니다
- **애그리게이트 루트**
    - 스터디(Study) 엔티티
- **구성 요소**
    - 스터디 멤버(StudyMember)
    - 모임 일정(Schedule)
    - 학습 자료(StudyMaterial)
- **불변식 예시**: "스터디 멤버 수는 정원을 초과할 수 없다", "모집이 마감된 스터디는 멤버를 추가할 수 없다"

아래의 스터디 애그리거트는 스터디의 핵심 비즈니스 규칙을 캡슐화합니다.
멤버 추가, 일정 관리 등의 동작이 모두 스터디 애그리게이트 루트를 통해서만 가능하며, 비즈니스 불변식(정원 초과 불가, 모집 중인 스터디만 멤버 추가 가능 등)을 보호합니다. 외부에서는 스터디 내부 구성요소에 직접 접근할 수 없고, 스터디 애그리게이트 루트를 통해서만 상호작용할 수 있습니다.

java

```java
public class Study {
    private StudyId id;
    private UserId organizerId;
    private List<StudyMember> members;
    private List<Schedule> schedules;
    private Capacity capacity;
    private StudyStatus status;
    
	    // 애그리거트 루트를 통해서만 맴버를 추가 할 수 있습니다
    public void addMember(User user) {
        validateRecruitmentOpen();
        validateCapacityNotExceeded();
        
        StudyMember member = new StudyMember(id, user.getId(), MemberRole.MEMBER);
        members.add(member);
        
        if (members.size() >= capacity.getValue()) {
            closeRecruitment();
        }
    }
    
    // 비즈니스 불변식 보호
    private void validateRecruitmentOpen() {
        if (status != StudyStatus.RECRUITING) {
            throw new StudyStatusNotMatchedException();
        }
    }
    
    private void validateCapacityNotExceeded() {
        if (members.size() >= capacity.getValue()) {
            throw new StudyCapacityException();
        }
    }
    
    public void addSchedule(LocalDateTime dateTime, String location, String topic) {
        validateStudyNotCompleted();
        Schedule schedule = new Schedule(id, dateTime, location, topic);
        schedules.add(schedule);
    }
    
    private void validateStudyNotCompleted() {
        if (status == StudyStatus.COMPLETED) {
            throw new StudyCompletedException();
        }
    }
    
    private void closeRecruitment() {
        this.status = StudyStatus.CLOSED;
        // 도메인 이벤트 발행
        DomainEvents.publish(new StudyRecruitmentClosed(this.id));
    }
}
```


## 컨텍스트 맵 (Context Map)

----
컨텍스트 맵은 시스템 내의 여러 바운디드 컨텍스트 간의 관계를 정의하고 시각화하는 도구입니다.
### 주요 특징
- **관계 문서화**: 바운디드 컨텍스트 간의 관계를 명시적으로 문서화합니다.
- **통합 패턴 정의**: 컨텍스트 간 통합 방식을 정의합니다.
- **전체 시스템 이해**: 시스템의 전체 아키텍처를 이해하는 데 도움이 됩니다.
- **팀 간 협업**: 서로 다른 팀 간의 책임과 협업 방식을 명확히 합니다.

### 통합 패턴 ( 참고용 )
1. **공유 커널(Shared Kernel)**: 두 컨텍스트가 일부 모델을 공유합니다.
2. **고객-공급자(Customer-Supplier)**: 한 컨텍스트가 다른 컨텍스트에 종속됩니다.
3. **준수자(Conformist)**: 한 컨텍스트가 다른 컨텍스트의 모델을 그대로 따릅니다.
4. **부패 방지 계층(Anti-Corruption Layer)**: 한 컨텍스트가 다른 컨텍스트의 모델로부터 자신을 보호합니다.
5. **오픈 호스트 서비스(Open Host Service)**: 컨텍스트가 다른 컨텍스트들이 사용할 수 있는 서비스 집합을 제공합니다.
6. **발행된 언어(Published Language)**: 컨텍스트 간 통신을 위한 공통 언어를 정의합니다.

### 실제 예시 -  스터디 플랫폼 시스템에서의 컨텍스트 맵

- **스터디 관리-등록 관계**: 스터디 관리 컨텍스트는 등록 컨텍스트의 공급자로, 등록 컨텍스트가 필요로 하는 스터디 정보(모집 상태, 정원 등)를 제공합니다(고객-공급자 관계).
- **등록-사용자 관계**: 등록 컨텍스트는 사용자 컨텍스트의 모델을 그대로 따릅니다. 사용자 프로필 정보를 변환 없이 활용하며, 사용자 컨텍스트의 변경에 맞춰 자신을 조정합니다(준수자 관계).
- **학습 자료-외부 저장소 관계**: 학습 자료 컨텍스트는 외부 클라우드 저장소(Google Drive, Dropbox 등)와 통신할 때 발행된 언어(표준화된 파일 메타데이터 형식)를 사용합니다.

## 바운디드 컨텍스트 | 애그리거트 | 컨텍스트맵

-----

1. **바운디드 컨텍스트 내에 애그리게이트**: 각 바운디드 컨텍스트는 여러 애그리게이트를 포함합니다. 예를 들어, 주문 컨텍스트는 주문, 결제, 배송 애그리게이트를 포함할 수 있습니다.
2. **컨텍스트 맵으로 바운디드 컨텍스트 연결**: 컨텍스트 맵은 여러 바운디드 컨텍스트 간의 관계를 정의하여 전체 시스템의 구조를 명확히 합니다.
3. **애그리게이트 간 참조**: 서로 다른 바운디드 컨텍스트의 애그리게이트는 일반적으로 ID를 통해 간접적으로 참조합니다. 직접적인 객체 참조는 컨텍스트 간의 결합도를 높일 수 있습니다.

## 이벤트 스토밍 예시
![[스크린샷 2025-05-12 오후 11.11.56.png]](./images/study-event-stroming.webp)