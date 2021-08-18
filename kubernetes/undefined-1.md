# 쿠버네티스 애플리케이션 관리

### 컨테이너 이미지를 잘 만들자

애플리케이션을 이미지로 패키징할 때 애플리케이션의 바이너리 실행파일과 필요한 추가 라이브러리를 포함하는 것이 바람직하다. 불필요한 OS 파일시스템을 패키징하면 이미지를 크게 만든다.

Go 언어를 사용한 애플리케이션은 이미지에 바이너리 실행파일 외에 다른 것을 포함할 필요가 없다. 따라서 Go 기반 컨테이너의 경우 이미지 크기가 작기 때문에 쿠버네티스에 매우 적합하다.

 그러나 실제로 이런 최소한의 이미지만으로 디버깅하는 것은 매우 어렵다.

### 태그 지정 및 imagePullPolicy 설정을 잘하자

latest 태그는 개발에서만 사용하자. 그 이유는 latest로 새 버전을 푸시한 이후 디플로이먼트가 스케일 업하게 되면 새 버전으로 새 파드가 실행되기 때문이다.

imagePullPolicy를 `Always`로 하면 항상 이미지 레지스트리에 접근한다. 프로덕션 환경에서 이를 사용하면 파드 시작 속도가 약간 느려지고 레지스트리에 연결할 수 없는 경우 파드가 시작되지 않으므로 사용하지 않는 것이 좋다.

### 다차원 레이블을 사용하

파드 뿐만 아니라 모든 리소스에 레이블을 지정하자. 레이블 예로 다음과 같이 설정할 수 있다.

* 애플리케이션 이름
* 애플리케이션 계층\(frontend, backend 등\)
* 환경\(dev, qa, staging, test, production 등\)
* 버전
* 릴리스 유형\(canary, blue, green 등\)
* 테넌트
* 샤드

### 어노테이션을 사용하자

마이크로서비스 아키텍처의 경우 파드가 사용 중인 다른 서비스를 어노테이션을 사용하여 나열하는 것이 좋다. 이렇게 하면 파드 간 의존성을 파악할 수 있다.

### terminationMessagePath를 사용하자

컨테이너가 종료될 때 kubelet에서 읽고 kubectl describe pod의 출력에 표시된다.

프로세스가 메시지를 작성할 기본 파일은 `/dev/termination-log`이지만, 다음과 같이 `/var/termination-reason` 으로 변경할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-termination-message
spec:
  containers:
  - image: busybox
    name: main
    command:
    - sh
    - -c
    - 'echo "I''ll be back." > /var/termination-reason ; exit 1'
    terminationMessagePath: /var/termination-reason
```

kubectl describe 명령어를 통해 종료된 이유를 확인해보자.

```yaml
Name:         pod-with-termination-message
...
Containers:
...
    Command:
      sh
      -c
      echo "I'll be back." > /var/termination-reason ; exit 1
    State:      Terminated
      Reason:   Error
      Message:  I'll be back.

      Exit Code:  1
      Started:    Wed, 18 Aug 2021 01:53:56 +0000
      Finished:   Wed, 18 Aug 2021 01:53:56 +0000
    Last State:   Terminated
      Reason:     Error
      Message:    I'll be back.

      Exit Code:    1
      Started:      Wed, 18 Aug 2021 01:53:39 +0000
      Finished:     Wed, 18 Aug 2021 01:53:39 +0000
      ...
```

보다시피 프로세스가 /var/termination-reason 파일에 기록한 I'll be back. 메시지가 컨테이너의 Last State 섹션의 Message로 표시된다.

### 로깅 처리

kubectl logs 명령어로 로그를 쉽게 볼 수 있다. 이전 컨테이너의 로그를 보려면 --previous 옵션을 사용하면 된다.

#### 컨테이너 로그 및 파일 복사 방법

kubectl cp 명령어를 사용하면 가능하다. foo-pod 파드에 /var/log/foo.log 파일을 로컬로 복사하려면 다음 명령어를 사용하면 된다.

```yaml
kubectl cp foo-pod:/var/log/foo.log foo.log
```

로컬 컴퓨터에서 파드로 파일을 복사하려면 아래와 같이 실행한다.

```yaml
kubectl cp localfile foo-pod:/etc/remotefile
```

#### 로깅 스택

ELK 스택, EFK 스택 등이 있다. EFK 스택의 경우 FluentD 에이전트가 로그를 수집하고 ElasticSearch에 전달한다. 시각화 도구인 Kibana에서 로그를 보고 분석할 수 있다.

#### 여러 줄 로그 처리

자바의 스택 트레이스와 같이 여러 줄에 걸쳐 있는 로그를 처리하는 방법에는 애플리케잉션이 JSON을 출력하도록 설정하는 방법과 JSON 로그를 파일에 써서 플루언트디에서 처리하게 하는 방법이 있다. 두 번째 방법은 표준 출력으로 로그를 출력하기 때문에 kubectl logs로 봐도 보기 편하다.

