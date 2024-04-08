# CORS(Cross-Origin Resource Sharing)란?

브라우저가 다른 도메인의 자원을 안전하게 요청할 수 있도록 허용하는 HTTP 헤더 기반의 메커니즘이다.

### CORS 작동원리

웹 브라우저는 크로스 오리진 요청을 보내기 전에, 해당 리소스를 호스팅하는 서버로부터 접근을 허가받아야한다.
이를 위해 서버는 Access-Control-Allow-Origin과 같은 CORS 관련 HTTP 헤더를 응답에 포함시켜야 하며,
이 헤더는 서버가 어떤 origin의 웹 페이지로부터의 요청을 허용할 것인지를 명시한다.

### Preflight Request(사전요청)이란

CORS는 사전요청 메커니즘을 통해 실제 요청에 사용될 HTTP 메서드와 헤더를 서버 측에 전송한다.
이를 통해 서버는 해당 요청을 허용할지 여부를 결정하고, 이에 대한 응답을 브라우저에 전송한다.

### Same-Origin Policy

동일 출처 정책은 웹 보안의 원칙으로, 웹 페이지가 다른 출처의 리소스에 임의로 접근하거나 수정하는 것을 제한한다.
URL의 프로토콜, 포트, 호스트가 같을 때 동일한 출처를 갖는다고 판단된다.
동일 출처 정책은 데이터 보호에 중요한 역할을 하지만, 다양한 웹 서비스와 API를 통합하는 것에 제약이 되기도 한다.
보안을 유지하면서도, 이러한 제약을 극복하기 위해 CORS라는 방법이 고안되었다고 한다.

### 그럼 CORS 에러는 왜 발생하는가?

(내 해석이 많이 들어가서 잘못된 정보가 있을 수도 있음)

웹 브라우저는 기본적으로 동일 출처 정책을 고수한다. 단, 여기에 예외를 허용하는 방식이 CORS인 것이다.

- 앞선 사전 요청 단계에서 서버가 보내는 응답에 Access-Control-Allow-Origin 헤더가 없는 경우 브라우저가 CORS 에러를 발생시킨다.
- 헤더의 값이 요청 출처와 일치하지 않는 경우

개발 시에 마주했던 CORS 에러는 대부분 후자의 경우에 해당했던 것 같다.

  
### 요약
1. 브라우저는 기본적으로 동일 출처에서만 접근을 허용함. 그러나 서버의 API를 이용하기 위해서는 CORS가 필요
2. 브라우저는 서버에 사전요청을 보냄
3. 서버는 브라우저에게  Access-Control-Allow-Origin 헤더를 보내야함.
4. 브라우저는 이를 확인하고 서버에 요청을 보냄.

### 공부하고 느낀 점
CORS 정책의 구현은 서버 측에서 이루어지며, 서버는 어떤 출처의 요청을 허용할지 결정한다. 하지만 이것은 서버 자체의 보호보다는 브라우저를 통해 서버에 접근하는 사용자를 보호하기 위한 것이라는 점이 새로웠다. 막연하게 서버 자체를 보호하기 위한 것이라고 착각하고 있었다.

참고한 자료
- https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
- https://developer.mozilla.org/ko/docs/Web/HTTP/Methods/OPTIONS
- https://developer.mozilla.org/ko/docs/Glossary/Same-origin_policy
- https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors
- https://velog.io/@awesome-hong/HTTP-Options-%EC%9A%94%EC%B2%AD%EC%9D%80-%EB%AD%98%EA%B9%8C
