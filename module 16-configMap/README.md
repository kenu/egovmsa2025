Config Map 
===

1. [용어정리](#용어정리)
2. [ConfigMap 정의](#configmap)
3. [특징](#특징)
4. [생성 방법](#생성-방법)
5. [구조 및 주요 필드](#구조-및-주요-필드)
6. [Pod에서 사용하는 방법](#pod에서-사용하는-방법)
7. [요약 | secret | 참고](#요약)

## 용어정리

### ✅ Pod(파드)
: 쿠버네티스에서 하나의 프로그램을 실행시키는 단위
→ docker에서의 컨테이너에 대응, 더욱 유연한 구조

- Pod 1개는 1개 이상의 컨테이너로 구성
** 보통은 1개의 컨테이너를 가짐
- 휘발성이 매우 강함 : 재시작 시 초기화
- 쿠버네티스는 이미지 기반 파드 실행 
→ docker와 동일한 방식

### ✅ Volume(볼륨)
: 컨테이너에서, 컨테이너 간의 데이터를 저장 및 공유하는 공간 - 추상적 개념

- 데이터 지속성 보장 : Pod 재시작 후에도 유지
- 공유 저장소 : 여러 컨테이너가 같은 데이터 공유 가능

## ConfigMap

: [key-value] 쌍으로 기밀이 아닌 데이터를 저장하는 데 사용하는 API 오브젝트

: 애플리케이션이 필요한 구성정보를 클러스터 내에서 쉽게 관리하고 애플리케이션 코드와 설정 정보를 분리하기 위해 사용

→ 설정 정보를 하드코딩이 아닌 외부관리를 할 수 있도록 해준다

ex) 앱에서 이름 또는 색상 같은 설정을 바꿀 때 코드 수정 X, config map만 바꾸면 된다

### 특징

- 텍스트 형태의 데이터 → **`data`**(UTF-8 문자열) 또는 **`binaryData`**(base64 인코딩 바이너리) 필드에 저장
- 주입 방식 : Pod(파드)에서 ConfigMap의 데이터를 전달하는 방식
    - 환경 변수
    - Volume(볼륨)으로 마운트해 설정 파일로 사용
    - 컨테이너 명령줄 인자로 전달
    - Kubernetes API 직접 읽기
    - 등등
- Immutable(불변) 옵션 : `immutable : true`로 설정해 변경 불가능한 ConfigMap 만들 수 있음 - v1.19 이상
→ 수정 불가, 삭제 후 재생성만 가능
- ⚠️ 데이터 용량 제한 : 하나의 Config Map은 1MiB(메비바이트)를 초과하는 데이터 저장 불가능
- ⚠️ 보안 X : Comfig Map은 보안이나 암호화 제공 X
→ 비 민감 정보 사용 : API URL, 환경 이름 등
→ 민감 정보는 Secret 사용 권장 :  비밀번호·API키

### 생성 방법

- 명령어 사용
    
    1) 키-값 쌍으로 직접 생성
    
    ```bash
    kubectl create configmap my-config --from-literal=key=value
    ```
    
    - my-config : ConfigMap 이름
    - key=value : 저장할 데이터
    
    2) 파일 내용을 읽어 생성
    
    ```bash
    kubectl create configmap my-config --from-file=app.properties
    ```
    
    ex) 
    
    `app.properties`
    
    ```bash
    username = user
    password = 0000
    ```
    
    ⬇️
    
    yaml
    
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: my-config
    data:
      app.properties: |
        username=user
        password=0000
    
    ```
    
    3) YAML 파일로 생성
    
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: my-config
    data:
      key1: value1
      config.properties: |
        setting1=true
        setting2=false
    ```
    
    ** | : 여러 줄 문자열 표시  
    
### 구조 및 주요 필드

- 기본 YAML 구조

```yaml
apiVersion: v1 # Kubernetes API 버전 - 항상 v1
kind: ConfigMap # 오브젝트 종류
metadata: # 이름, 네임스페이스 등 메타 데이터
  name: my-config
  namespace: default
data: # 설정 데이터 [key - value]
  key1: value1
  key2: value2
binaryData: # (선택)binaryData : 바이너리 데이터 저장용 필드
	file.bin: <base64 인코딩된 데이터>
immutable: true  # 선택사항 (v1.19+)  

```

- 주요 필드
    - metadata : ConfigMap의 이름, 네임스페이스, 레이블 등 메타데이터 정보
    - data : UTF-8 문자열로 저장되는 키-값 쌍
    → 일반적인 설정 값 저장에 사용
    - binaryData : base64 인코딩된 바이너리 데이터를 저장하는 키-값 쌍
    → 바이너리 파일 또는 특수 데이터에 사용
- key 규칙
    - key : 영어, 숫자, `-`, `_`, `.`만 허용
    - data와 binaryData에 같은 키 동시 사용 X

### Pod에서 사용하는 방법

- 환경 변수로 주입
: 특정 key값을 환경변수로 전달

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key1: value123
```

```yaml
env:
  - name: SETTING # 컨테이너 안에서 사용할 환경 변수 이름
    valueFrom:
      configMapKeyRef: # ConfigMap에서 값을 가져올 것임을 의미
        name: my-config # 참조할 Config 이름
        key: key1 # Config 안의 Key 값을 가져옴

```

⇒ `$SETTING`을 참조하면 `value123`

```bash
echo $SETTING
# -> value123 출력
```

- 파일처럼 마운트
: ConfigMap 전체를 컨테이너 내부 디렉토리에 파일로 마운트

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  key1: hello
  key2: world
```

```yaml
volumes: 
  - name: config-volume # 사용할 볼륨 이름 정의
    configMap:
      name: my-config # 해당 볼륨이 참조할 ConfigMap 이름
volumeMounts:
  - name: config-volume # 위에서 정의한 볼륨 이름
    mountPath: /etc/config # 파일 형태로 붙일 경로
```

⇒ 컨테이너 내부에 아래와 같은 파일 생성
** key : 파일 이름 / value : 파일 내용

```bash
/etc/config/key1    # 내용: hello  
/etc/config/key2    # 내용: world
```

### 요약

ConfigMap은 코드 수정 없이 설정을 바꿀 수 있게 해주는 도구

### + **Secrets**

: 민감한 정보를 저장하고 사용하는 데 특화된 오브젝트 [key-value]

- 비밀버호, API 키, 인증 토큰 등을 저장할 때 사용
- base64로 인코딩
- ConfigMap과 유사하지만 보안에 초점을 둠

### 참고

https://www.youtube.com/watch?v=5rYodlzojHk&list=PLeWIi-8hd-6WRfohXdF93aPfbaJdf6h96&index=16

https://kubernetes.io/ko/docs/concepts/configuration/configmap/

https://kubernetes.io/docs/concepts/configuration/secret/