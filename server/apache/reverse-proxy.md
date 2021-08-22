# mod\_proxy

## ProxyPass와 ReverseProxyPass

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

요청을 처리한 후 리다이렉트 등의 이유로 URL이 아래와 같이 수정되어 리턴되었다고 해보자.

```text
http://backend.com/baz
```

위 URL은 ProxyPassReverse 설정에 의해서 아래 URL로 처리되어 Client 쪽 전달헤더에도 아래 주소가 전달된다.

```text
http://reverseproxy.com/foo/bar/baz
```

ProxyPassReverse가 없다면 `http://backend.com/baz`와 같이 리다이렉트 될 것이며 브라우저는 해당 URL을 바라보게 된다.

이러한 이유로 함께 설정하는 것이 좋한다.

## ProxyPreserveHost

이 옵션을 선택하면 ProxyPass 행에 지정된 호스트 이름 대신 들어오는 요청에서 프록시 호스트로 Host: 부분을 전달한다.

ProxyPreserveHost는 Apache가 원래 호스트 헤더를 백엔드 서버로 전달하도록 합니다. 이것은 백엔드 서버가 응용프로그램에 액세스하는 데 사용되는 주소를 인식하도록 하기 때문에 유용합니다.

## ProxyRequests

ProxyRequests 지시자는 ProxyPass를 사용하면 해제한다. forward proxy를 사용할 경우 On으로 설정한다.



