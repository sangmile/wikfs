# Reverse Proxy

## mod\_proxy Module

ProxyPass와 ReverseProxyPass 지시자를 함께 사용하자.

ProxyPass 지시자는 수신 요청을 백엔드 서버에 매핑하도록 지정한다. ReverseProxyPass 지시자는 백엔드 서버에서 처리된 요청을 자기자신에게 보내는 대신 리버스 프록시 서버로 보낸다.

예를 들어 다음과 같이 설정되어 있다고 하자.

```text
ProxyPass "/foo/bar" "http://backend.com/bar"
ProxyReversePass "/foo/bar" "http://backend.com/bar"
```

아래 url로 리버스 프록시 서버로 요청이 오면,

```text
http://reverseproxy.com/foo/bar
```

아래 url로 보내진다.

```text
http://backend.com/bar
```

요청을 처리한 후 리다이렉트 등의 이유로 URL이 아래와 같이 수정되어 리턴었다고 하자.

```text
http://backend.com/baz
```

위 URL은 ProxyPassReverse 설정에 의해서 아래 URL로 처리되어 Client 쪽 전달헤더에도 아래 주소가 전달될 것이다.

```text
http://reverseproxy.com/foo/bar/baz
```

ProxyPassReverse가 없다면 `http://backend.com/baz`와 같이 리다이렉트 될 것이며 브라우저는 해당 URL을 바라보게 된다.

이러한 이유로 함께 설정하는 것을 권장한다.

