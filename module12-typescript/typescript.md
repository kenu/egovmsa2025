# 타입스크립트와 MSA

## 1. 타입스크립트란?

2012년 마이크로소프트에서 개발한 언어로 약타입 언어인 자바스크립트의 타입 안정성을 보강하기 위해 개발됨.

초창기 자바스크립트는 정적 문서안에서 단순한 스크립팅을 위해 간소하게 개발된 언어였으나, 웹의 발전과 함께 자바스크립트의 사용량도 증가하며 각 종 인터렉션과 상호작용등의 복잡화, 대규모화에따라 자바스크립트의 타입 안정화 필요성 대두.

### - 주요 특징
- 컴파일을 통한 자바스크립트 타입검사
- 제네릭스, 상속, 인터페이스 등의 기능 제공

### - 기본 사용 방법
1) 타입스크립트 설치 및 .ts 파일 생성
2) .ts파일 소스코드 작성
```typescript
1. 변수타입 선언
let a = 0; //자바스크립트에서의 변수 선언 및 값할당
let a:number = 0; // 타입스크립트 변수 선언 및 값 할당 (타입 미선언시 컴파일 에러 발생)

2. 함수타입 선언
function sum(x:number, y:number):number{ // 함수에서의 타입선언
    return x+y;
}
const sum = (a: number, b: number): number => { //화살표 함수시
  return a + b;
};

3. 타입 별칭 (type alias, 객체형 타입)
type Person = { // 객체형 타입인 Person 선언
  name: string;
  age: number; 
};

const person: Person = { //Person 타입인 변수 person 선언
  name: 'seongugu'
  age : 36
};

4. 유니언 타입
let input: string || number;

input = "hello"; 
input = 123;     
input = true; // 컴파일시 타입에러 발생

5. 리터럴 타입
type hello = "hello";
let a: hello = "hello";
let b: hello = "hi"; // 컴파일시 타입에러 발생

6. 제네릭
function abc<T>(value: T): T {
  return value;
}

const test1 = abc<number>(123);   
const test2 = abc<string>("hi");
const test3 = abc<number>(123); // 컴파일시 타입에러 발생
```

3) 컴파일 (tsc 명령어 사용하여 js파일 생성)



## 2. 타입스크립트 vs 자바스크립트

| 항목 | JavaScript | TypeScript |
|------|------------|------------|
| 타입 시스템 | 동적, 약타입 | 정적, 강타입 |
| 실행 방식 | 인터프리터 | 컴파일 후 자바스크립트 실행 |
| 오류 발견시점 | 런타임 시 | 컴파일 타임 시 |
| 유지보수성 | 어려움(대규모시)  | 용이함 |
| 기타 | 학습 용이 | 러닝커브 존재



## 3. MSA에서의 타입스크립트
- MSA에서의 front-end는 모듈화된 백엔드의 각 도메인들과 api로 통신하며 UI 렌더링 등에 필요한 데이터 요청
- 이때 타입 안정성 없는 데이터를 기반으로하는 통신이라면?

문제 예시1) 프론트에서 특정 키&값을 누락한 객체를 post요청
```javascript
const payload = {
  userId: 123,
  productId: 456,
  // price 누락됨
};

axios.post("/order-service/create", payload);

//백엔드 데이터 정합성을 해치게되는 요청이지만 런타임에서만 확인가능
// payload의 타입을 interface등을 통해 사전 정의해놓았다면 price누락 컴파일시 확인가능
```

문제 예시2) 같은 정보를 여러 서비스에서 다른방식으로 다룸
```javascript
// 서비스 A
user.name

// 서비스 B
user.fullName

// 서비스 C
user.username

// 셋다 유저의 이름을 다루지만 서비스별 공통 인터페이스없음
// type user에 대해 타입 정의해놓으면 공통으로 간편하게 유지보수 가능
```


## 4. 결론
여럿이 대규모로 복잡하게 일하는 환경이면 반드시 지켜야하는 규약 **(타입)** 을 만들고 안지키면 지키게 강제해야 **(컴파일)** 일이 문제없이 잘 처리되고 수월하게 진행된다.

---
### 참고자료
타입스크립트 공식문서 : 
https://www.typescriptlang.org/ko/docs/handbook/

