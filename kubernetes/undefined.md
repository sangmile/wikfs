# 파드 라이프사이클

## 파드 라이프사이클

### 1. 파드 재배치

쿠버네티스 환경이 아닌 가상머신에서 실행되는 애플리케이션은 한 시스템에서 다른 시스템으로 이동하는 경우가 드물다. 쿠버네티스를 사용하면 애플리케이션이 훨씬 더 자주 자동으로 재배치된다.

파드가 재배치될 때 고려해야할 항목들이다.

* 로컬 IP와 호스트 이름 변경
* 디스크에 기록된 데이터

#### 로컬 IP와 호스트 이름 변경

스테이트풀 애플리케이션을 스테이트풀셋으로 실행하더라도 호스트 이름과 퍼시스턴트 상태는 동일하지만 파드의 IP는 변경될 것이다. 따라서 호스트 이름을 기반으로 한 스테이트풀셋을 사용해야 한다.

#### 디스크에 기록된 데이터

프로세스 크래시나 Liveness Probe가 실패를 반환했거나 노드의 메모리 부족에 의해 종료된 경우와 같이 컨테이너가 다시 시작될 수 있다. 이 경우 파드는 여전히 동일하지만 컨테이너 자체는 완전히 새로운 것이다.

컨테이너를 재시작할 때 파일을 보존하려고 볼륨을 사용하는 것이 항상 좋은 방법이 아니다. 저장된 데이터 자체가 손상되면 연속 크래시 루프가 발생한다. 볼륨을 사용하지 않았다면 새 컨테이너가 처음부터 시작돼 크래시하지 않을 가능성이 높다. 볼륨 사용 여부를 신중하게 생각해야 한다.

### 2. 파드 재스케줄링

파드의 컨테이너가 계속 크래시되면 kubelet은 계속 파드를 재시작한다. 재시작 간격은 5분이 될 때까지 간격이 증가한다. 레플리카셋 컨트롤러는 파드가 죽은 상태인지 아닌지 관심이 없다. 파드 수가 의도하는 레플리카 수와 일치하는지에만 관심이 있다.

### 3. 초기화 컨테이너

파드를 초기화하는 데 사용한다. 이는 주 파드의 볼륨에 데이터를 쓴 다음 주 컨테이너에 마운트하는 것을 의미한다.

파드에 초기화 컨테이너를 추가해보자.

{% code title="fortune-client.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fortune-client
spec:
  initContainers:
  - name: init
    image: busybox
    command:
    - sh
    - -c
    - 'while true; do echo "Waiting for fortune service to come up..."; wget http://fortune -q -T 1 -O /dev/null >/dev/null 2>/dev/null && break; sleep 1; done; echo "Service is up! Starting main container."'
  containers:
  - image: busybox
    name: main
    command:
    - sh
    - -c
    - 'echo "Main container started. Reading fortune very 10 seconds."; while true; do echo "-------------"; wget -q -O - http://fortune; sleep 10; done'

```
{% endcode %}

fortune 서비스와 fortune 서버 파드를 배포할 때까지 주 컨테이너가 실행되지 않는다.

{% code title="fortune-server.yaml" %}
```yaml
apiVersion: v1
kind: Service
metadata:
  name: fortune
spec:
  selector:
    app: fortune-server
  ports:
  - name: http
    port: 80
    targetPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  name: fortune-server
  labels:
    app: fortune-server
spec:
  containers:
  - image: sangmile/fortune:args
    args: ["2"]
    name: html-generator
    volumeMounts:
    - name: html
      mountPath: /var/htdocs
  - image: nginx:alpine
    name: web-server
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
      readOnly: true
    ports:
    - containerPort: 80
      protocol: TCP
  volumes:
  - name: html
    emptyDir: {}
```
{% endcode %}

#### 파드 간 의존성 처리를 위한 모범 사례

전제 조건이 충족될 때까지 파드 컨테이너의 주 컨테이너의 시작을 지연시키는 데 초기화 컨테이너를 사용하는 방법을 살펴봤다. 의존해야 할 서비스를 필요로 하지 않도록 애플리케이션을 만드는 것이 좋은 방법이며, 내부적으로 준비가 되었는지 확인할 수 있도록 처리해야 하고 이를 위해 레디니스 프로브를 사용하는 것이 바람직하다.

### 4. 라이프사이클 훅 추가

두 가지 라이프사이클 훅을 추가할 수 있다.

* post-start hook
* pre-stop hook

다음을 수행할 수 있다.

* 컨테이너 내부에서 명령 실행
* URL로 HTTP GET 요청

#### post-start lifecycle hook

훅이 완료될 때까지 컨테이너는 ContainerCreating인 채로 Waiting 상태가 유지된다. 이 때문에 파드 상태는 Running 중이 아니라 Pending 상태다. 훅이 실행되지 않거나 0이 아닌 종료 코드를 반환하면 주 컨테이너가 종료된다.

{% code title="pod-start-hook.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-poststart-hook
spec:
  containers:
  - image: sangmile/kumile
    name: kumile
    lifecycle:
      postStart:
        exec:
          command: 
          - sh
          - -c
          - "echo 'hook will fail with exit code 15'; sleep 5 ; exit 15"

```
{% endcode %}

일반적으로 컨테이너 이미지에 저장된 셸 스크립트 또는 바이너리 실행파일을 실행한다.

HTTP GET 훅 핸들러를 사용하는 예이다.

{% code title="post-start-hook-httpget.yaml" %}
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-poststart-hook
spec:
  containers:
  - image: sangmile/kumile
    name: kumile
    ports:
    - containerPort: 8080
      protocol: TCP
    lifecycle:
      postStart:
        httpGet:
          port: 9090               # 의도적으로 9090번으로 잘못 구성함.
          path: postStart

```
{% endcode %}

#### pre-stop lifecycle hook

컨테이너가 종료되기 직전에 pre-stop hook이 실행된다. kubelet은 pre-stop hook을 실행한 다음 SIGTERM을 전송한다.

pre-stop lifecycle hook은 훅 결과에 상관없이 종료된다.

애플리케이션이 kubelet에서 보낸 SIGTERM 신호를 수신하지 못하기 때문에 pre-stop hook을 실행한다. 컨테이너 이미지가 셸을 실행하도록 구성되어 셸에서 애플리케이션 프로세스를 실행하는 경우 자식 프로세스로 전달되지 않고 셸 자체에서 신호를 차단할 수 있다. 그 예는 다음과 같이 dockerfile에 정의된 경우다.

```yaml
ENTRYPOINT node app.js
```

하지만 신호를 애플리케이션에 직접 보내기 위해 종료 전 훅을 추가하는 대신 셸이 신호를 애플리케이션에 전달하도록 하는 것이 올바른 방법이다. 컨테이너 이미지가 셸을 실행하지 않고 애플리케이션을 직접 실행하도록 다음과 같이 구성한다.

```yaml
ENTRYPOINT ["node", "app.js"]
```

post-start hook과 pre-stop hook은 파드가 아닌 컨테이너와 관련이 있음을 기억하자.

### 5. 파드 셧다운

컨테이너를 종료하는 시간은 제한되어 있다. 이 시간을 종료 유예 기간\(termination grace period\)이라고 한다. 순서는 다음과 같다.

1. pre-stop hook 실행 후 완료될 때까지 기다린다.
2. SIGTERM 신호를 컨테이너의 주 프로세스로 보낸다.
3. 컨테이너가 완전히 종료될 때까지 또는 종료 유예 기간이 끝날 때까지 기다린다.
4. 정상적으로 종료되지 않은 경우 SIGKILL로 프로세스를 강제 종료한다.

![](../.gitbook/assets/image%20%2822%29.png)

종료 유예 기간은 terminationGracePeriodSeconds 필드로 설정할 수 있다.



## 클라이언트 요청의 적절 처리 보장

클라이언트 관점에서 파드의 라이프사이클을 이해하는 것도 중요하다. 파드를 스케일 업 및 스케일 다운할 때 클라이언트가 문제를 겪지 않게 해야 하기 때문이다.

예상되는 문제는 두 가지이다.

* 파드가 시작될 때 클라이언트 연결
* 파드가 종료될 때 연결 끊어

#### 파드가 시작될 때

파드가 준비되어야만 서비스의 엔드포인트에 추가되어 클라이언트로부터 요청을 받는다. 이 문제는 레디니스 프로브를 지정하여 해결할 수 있다.

#### 파드가 종료될 때

API 서버가 파드 삭제 요청을 받으면 먼저 etcd의 상태를 수정한 다음 kubelet과 엔드포인트 컨트롤러에게 삭제를 알린다. 이 두 가지 이벤트는 동시에 발생한다.

![](../.gitbook/assets/image%20%2821%29.png)

iptables 규칙이 모든 노드에서 업데이트되기 전에 SIGTERM 신호가 전송될 가능성이 높다. 결국 파드는 종료 신호를 보낸 후에도 클라이언트 요청을 받을 수 있다는 얘기다. 클라이언트가 "Connection Refused" 오류를 수신하게 된다.

위 문제를 완벽히 해결할 수는 없지만 SIGTERM 신호가 전송되는 시점을 pre-stop lifecycle hook을 통해 5초 또는 10초 정도의 지연 시간을 추가하면 사용자 경험이 상당히 향상될 수 있음을 기억하자.

```yaml
lifecycle:
  preStop:
    exce:
      command:
      - sh
      - -c
      - "sleep 5"
```

## 

