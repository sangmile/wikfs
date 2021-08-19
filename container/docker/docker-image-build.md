# Docker Image Build

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

