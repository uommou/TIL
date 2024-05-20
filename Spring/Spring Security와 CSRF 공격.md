# Spring Security
Spring 기반 애플리케이션의 보안을 담당하는 프레임워크로, 인증, 권한 부여 및 일반적인 공격으로부터 보호하는 기능을 제공한다.

1. 인증 (Authentication)
인증은 사용자가 누구인지 확인하는 것이다.
Spring Security는 다양한 인증 메커니즘을 지원하는데, 
폼 기반 인증, 쿠키 기반 인증, 소셜 로그인(OAuth2), JSON Web Tokens (JWT) 등이 있다.

2. 인가 (Authorization)
인가는 인증된 사용자가 특정 리소스에 접근할 수 있는 권한이 있는지를 설정한다.
Spring Security는 권한 설정을 통해 특정 역할을 가진 사용자만이 특정 기능을 사용할 수 있도록 제한할 수 있다.
예를 들어 사이트의 ADMIN과 USER 권한을 다르게 설정할 수 있을 것이다.

3. 일반적인 공격에 대한 보호
Spring Security는 CSRF (Cross-Site Request Forgery), XSS (Cross-Site Scripting), SQL 인젝션 등의 일반적인 웹 공격으로부터 애플리케이션을 보호할 수 있다고 한다.

그럼 본격적으로 CSRF가 무엇인지, Spring Security에서는 이를 어떻게 방지하는지 알아보자.

## CSRF (Cross-Site Request Forgery)
  
  ![image](https://github.com/uommou/TIL/assets/90598552/939285d1-c1a4-4907-832b-0ee1eb7a2b73)
  
  이미지 출처 : https://medium.com/@rajeevranjancom/cross-site-request-forgery-csrf-attack-6949edb9e405

  사용자의 웹 브라우저를 이용하여 사용자가 의도하지 않은 행동을 서버에 요청하도록 만드는 보안 공격이다.

  1. 피해자가 웹사이트에 로그인하고 세션 쿠키가 생성된다.
  2. 공격자는 악성 웹사이트 또는 이메일을 통해 피해자가 클릭할 수 있는 링크 또는 자동으로 요청을 전송하는 폼을 생성한다.
  3. 피해자가 악성 링크를 클릭하거나 악성 웹페이지에 방문하게 되면, 브라우저는 자동으로 공격자가 준비한 요청을 취약한 웹사이트로 전송한다.
  4. 웹사이트는 이 요청을 합법적인 사용자의 요청으로 인식하고, 세션 쿠키를 기반으로 요청을 처리한다. 사용자가 의도하지 않은 행동(비밀번호 변경, 자금 이체 등)이 이루어진다.

  **그럼 Spring Security 에선 이 공격 방법을 어떻게 방지할까?**

  ### 동기화 토큰 패턴
  ![image](https://github.com/uommou/TIL/assets/90598552/762a549c-322b-43be-af6c-01144d6f2989)

  이미지 출처 : https://www.javadevjournal.com/spring-security/spring-security-csrf-token/

  Spring Security 는 **Synchronized Token Pattern**(동기화 토큰 패턴)이라는 것을 사용하여 CSRF 공격을 방지한다고 한다.
  그 외에도 쿠키에 Same Site 속성을 부여한다고 하는데, 일단은 Synchronized Token Pattern 먼저 알아보자.

  1. 각 HTTP 요청에는 세션 쿠키 외에도 CSRF 토큰이라는 안전한 무작위 생성 값이 필요하다.
  2. 서버는 이 토큰을 서버 측에 저장하고 클라이언트에도 전달한다.
  3. 이 CSRF 토큰은 클라이언트가 HTTP 요청을 보낼 때 포함되어 있어야 한다.
  4. HTTP 요청이 오면 서버는 서버 측에 저장한 토큰과 요청에 포함된 토큰을 비교한다.
  5. 토큰 값이 일치하지 않는 경우 해당 요청은 위조 요청으로 간주되어 거부된다.
  
  GET 요청에 스프링 보안 CSRF 토큰을 포함할 경우 유출 위험이 있어, GET 요청에는 토큰을 포함하지 않는다고 한다.

  **[Spring Security에서 CSRF 토큰을 활성화하는 법]**
  ```java
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable();
  }
  ```

  **[웹에 숨겨진 CSRF 토큰]**
  ```html
  <form method="post"
  	action="/transfer">
  <input type="hidden"
  	name="_csrf"
  	value="4bfd1575-3ad1-4d21-96c7-4ef2d9f86721"/>
  <input type="text"
  	name="amount"/>
  <input type="text"
  	name="routingNumber"/>
  <input type="hidden"
  	name="account"/>
  <input type="submit"
  	value="Transfer"/>
  </form>
  ```

  동일 출처 정책(Same-Origin Policy) 때문에 공격자는 CSRF 토큰을 읽을 수 없다고 한다.
  
  사용자가 웹사이트에서 로그인하여 CSRF 토큰을 받게 되면, 이 토큰은 사용자의 브라우저에서만 접근 가능하고 다른 출처를 가진 공격자의 웹 사이트는 이 토큰을 읽거나 수정할 수 없다.
  
  공격자가 다른 도메인에서 악성 스크립트를 호스팅하고 사용자가 그 스크립트를 실행하게 만들었다 하더라도, 이 스크립트는 CSRF 토큰이 포함된 사용자의 세션 쿠키나 양식 데이터에 접근할 수 없는 것이다.
  
  결과적으로 공격자는 토큰 없이는 위조된 요청을 성공적으로 생성할 수 없으며, 서버는 위조된 요청을 거부한다.
  
  
  **[클라이언트 요청 예]**
  ```html
  POST /transfer HTTP/1.1
  Host: bank.example.com
  Cookie: JSESSIONID=randomid
  Content-Type: application/x-www-form-urlencoded
  
  amount=100.00&routingNumber=1234&account=9876&_csrf=4bfd1575-3ad1-4d21-96c7-4ef2d9f86721
  ```
  ### SameSite 속성
  쿠키가 어떤 상황에서 브라우저에 의해 전송되어야 하는지를 정의하여 CSRF 공격으로부터 웹 애플리케이션을 보호하는 방법이다. 이는 Spring Security에서 쿠키를 직접 제어하지 못하므로, 제공되지 않는 기능이라도 하니 간단하게 넘어가자^_^


### 참고자료
- https://docs.spring.io/spring-security/reference/servlet/architecture.html
- https://www.javadevjournal.com/spring-security/spring-security-csrf-token/
- https://blog.naver.com/orez_log/222619027250
