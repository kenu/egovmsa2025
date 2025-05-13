# 도메인 모델이란 무엇인가?

----

도메인 모델은 단순히 말해서 **현실 세계의 문제를 해결하기 위해 소프트웨어로 표현한 것**입니다.
도메인 모델의 핵심 특징은  단순화와 추상화압니다.

쇼핑몰을 예를 들어 설명하면,
- 현실: 손님이 가게에 가서 상품을 고르고 계산하는 과정
- 소프트웨어: 웹사이트에서 상품을 장바구니에 담고 결제하는 시스템

"메뉴부터 결제까지"의 전체 과정이 바로 도메인이고, 이것을 소프트웨어로 구현한 것이 도메인 모델입니다.

도메인 모델은 현실을 **단순화**해서 바라봅니다. 실제 가게에는 벽 색깔, 조명, 음악 등 수많은 요소가 있지만, 온라인 쇼핑몰에서는 이런 것들을 생략하고 **중요한 부분만** 남깁니다.

### 좀 더 간단한 예를 들면,
스터디 모임 사이트를 만든다고 가정해 보겠습니다.

### 현실 모델
- 스터디 모임 형성
    - 관심있는 주제로 사람이 모임
- 실제 스터디 진행
    - 진행자 혹은 모임장이 학습내용을 안내
    - 출석부 작성
    - 각자 준비해온 자료 발표
    - 진행자 혹은 모임장이 다음 모임의 과제 부여
- 애로 사항
    - 출석체크가 번거로움
    - 과제 제출 여부 확인이 힘듬
    - 새로운 멤버 전달할때 위 과정을 다시 겪어야됨

### 소프트웨어 비즈니스 로직으로 변환하면 ?

**소프트웨어에서는** 스터디 모임 형성부터, 실제 스터디 진행, 스터디 출석체크등을 소프트웨어로 구현

**현실의 복잡한 상황 처리**
1. **스터디 장소 변경**
    - 현실: 모든 사람에게 일일이 연락
    - 소프트웨어: `studySession.changeLocation("카페 강남 토즈")`
    - 자동으로 모든 멤버에게 푸시 알림 전송 및 카카오 단톡방에 전달

2. **과제 관리**
    - 현실: 종이에 적어서 다음 시간에 확인
    - 소프트웨어: `Assignment` 객체로 관리
3. 신규 멤버 온보딩
    - 현실: 기존 멤버가 새 사람에게 설명
    - 소프트웨어: 자동화된 온보딩 플로우
4. 시간과 공간의 제약 극복
    - 현실:
        - 모든 멤버가 같은 시간, 같은 장소에 있어야 함
        - 지각이나 결석 시 내용 놓침
        -  자료 공유가 제한적
    - 소프트웨어
        - 녹화본 링크, 자료 링크등 다양한 것들을 즉시 공유할 수 있음

### 코드 예시 ( 스터디 장소 변경 관련)

```java
// 현실의 "스터디 모임"을 소프트웨어로 표현
class StudyGroup {
    private StudyContents studyContents; // 스터디 컨텐츠등의 스터디 소개 
    private int maxMembers;              // 최대인원수
    private GroupStatus status;          // RECRUITING, ACTIVE, COMPLETED
    private List<StudySession> sessions; // 예정된 모든 세션들
    
    boolean join(Member newMember, memberValidator memberValidator) {
        membeValidator.validate();
        if (members.size() >= maxMembers) {
            raise MaxMemberException();
        }
        return true;
    }
}
```

```java
class StudySession { 
	private SessionTime sessionTime; // 스터디 시작시간과 종료시간 
	private SessionLocation sessionLocation;  //스터디위치
	private SessionContents sessionContents; // 세션 내용
	private Member facilitator; // 스터디 진행자 
	private Map<Member, AttendanceStatus> attendance; // 현실의 "출석부 체크"를 자동화 
	
	void updateLocation(SessionLocation sessionLocation){
		/// validate logic
		this.sessionsLcation = sesssionLocation;
	}
	
```


### 도메인과 코드: 현실 세계와 소프트웨어의 관계

도메인 주도 설계(DDD)의 핵심은 현실 세계의 도메인(업무 영역)을 소프트웨어 코드로 변환하는 과정입니다. 이는 단순한 복사가 아닌 창의적인 변환 과정입니다.


### 도메인 모델의 사이클
```
관찰 → 모델링 → 구현 → 피드백 → 재모델링 → 재구현 → ...
```

이 주기를 통해 코드는 점점 더 도메인을 정확하게 반영하게 됩니다. 이것이 바로 DDD의 핵심 가치 - 소프트웨어가 비즈니스 도메인과 함께 호흡하며 성장하는 것입니다.

이러한 과정을 통해 소프트웨어는 단순한 도구를 넘어 비즈니스의 핵심 자산이 됩니다.

## 변환 과정의 핵심 원칙

이런 변환 과정에서 중요한 것은
1. **본질 유지**: 현실의 핵심 개념은 그대로 유지하면서
2. **제약 극복**: 물리적, 시간적 제약을 기술로 해결
3. **효율성 증대**: 반복적이고 번거로운 작업을 자동화
4. **확장 가능성**: 현실에서 불가능한 기능도 추가 가능

## 지속적인 개선
처음에는 단순히 현실을 옮기는 것으로 시작하지만, 사용하면서 더 나은 방향으로 발전합니다
