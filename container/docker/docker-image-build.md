# Dockerfile

## 멀티스테이지 빌드

프로덕션 환경의 이미지는 최소한으로 필요한 실행 바이너리만 포함하는 것이 좋다. 이를 가능하게 하려면 멀티스테이지 빌드를 사용하면 된다.

첫번째 이미지는 빌드용 이미지이며, 두번째 이미지는 프로덕션 환경 이미지다.

```text
# ------------------------
# 1. Build Image
FROM golang:1.16 AS builder

# Install dependencies
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  

# Build modules
COPY app.go    ./
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .


# ------------------------
# 2. Production Image
FROM alpine:latest  
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app ./
CMD ["./app"]  
```

프로덕션 환경 이미지는 굳이 Go 이미지가 필요 없으므로 alpine 이미지를 베이스 이미지로 사용한다. 빌드용 Docker 이미지로 app이라는 이름의 실행 가능 바이너리 파일을 프로덕션 환경 이미지로 복사한다. 이때 --from 옵션을 사용하여 'builder'라는 이미지로부터 복사를 한다.

## 데몬 실행

이미지를 바탕으로 생성된 컨테이너 안에서 명령을 실행하려면 CMD 명령을 사용한다. Dockerfile에는 하나의 CMD 명령만 지정할 수 있다. CMD 명령은 세 가지 방법이 있다.

### Exec 형식

NGINX를 foreground에서 실행한다.

```text
CMD ["nginx", "-g", "daemon off;"]
```

이렇게 하면 컨테이너 내부에서 nginx 데몬을 직접 실행한다. 컨테이너 내부에서 프로세스 목록 조회 시 다음과 같다. nginx 데몬이 메인 프로세스다.

```text
# ps x
    PID TTY      STAT   TIME COMMAND
      1 ?        Ssl    0:00 nginx -g daemon off
     46 pts/0    Ss     0:00 /bin/sh
     54 pts/0    R+     0:00 ps x
```

### Shell 형식

쉘 형식은 쉘을 호출한다. 문자열을 인수로 지정할 때는 홑따옴표\('\)를 사용한다. 예\) /bin/sh -c

```text
CMD nginx -g 'daemon off;'
```

메인 프로세스\(PID 1\)는 nginx 프로세스가 아닌 shell 프로세스다.

```text
# ps x
    PID TTY          TIME CMD
			1 pts/0    00:00:00 /bin/sh -c nginx -g daemon off
 364851 pts/0    00:00:00 nginx -g daemon off
 364857 pts/0    00:00:00 ps x
```

### ENTRYPOINT 형식

ENTRYPOINT 명령의 인수로 CMD 명령을 사용할 수 있다.

ENTRYPOINT 명령으로는 컨테이너가 시작될 때 호출될 명령어를 지정하고 CMD 명령으로 ENTRYPOINT에 전달되는 인자를 정의한다.

fortune 애플리케이션에서 간격을 설정해보도록 하자. 간격을 명령줄 첫 번째 인자로 받는다.

{% code title="fortuneloop.sh" %}
```bash
#!/bin/bash
trap "exit" SIGINT

INTERVAL=$1
echo Configured to generate new fortune every $INTERVAL seconds

mkdir -p /var/htdocs

while :
do
  echo $(date) Writing fortune to /var/htdocs/index.html
  /usr/games/fortune > /var/htdocs/index.html
  sleep $INTERVAL
done
```
{% endcode %}

도커파일 내용은 다음과 같다. CMD 명령을 통해 인자 값을 10으로 설정한다.

{% code title="Dockerfile" %}
```text
FROM ubuntu:latest

RUN apt-get update ; apt-get -y install fortune
COPY fortuneloop.sh /bin/fortuneloop.sh

RUN ["chmod", "+x", "/bin/fortuneloop.sh"]
ENTRYPOINT ["/bin/fortuneloop.sh"]
CMD ["10"]
```
{% endcode %}

도커 이미지를 빌드한 후 인자 값을 지정하지 않고 실행하면 기본값인 10이 적용된다.

```text
$ docker run -it sangmile/fortune:args

Configured to generate new fortune every 10 seconds
Mon May 31 07:34:01 UTC 2021 Writing fortune to /var/htdocs/index.html
Mon May 31 07:34:11 UTC 2021 Writing fortune to /var/htdocs/index.html
Mon May 31 07:34:21 UTC 2021 Writing fortune to /var/htdocs/index.html
Mon May 31 07:34:31 UTC 2021 Writing fortune to /var/htdocs/index.html
```

args 값을 설정해서 실행하면 제대로 전달이 된다.

```text
$ docker run -it sangmile/fortune:args 15

Configured to generate new fortune every 15 seconds
Mon May 31 07:35:28 UTC 2021 Writing fortune to /var/htdocs/index.html
Mon May 31 07:35:43 UTC 2021 Writing fortune to /var/htdocs/index.html
```

