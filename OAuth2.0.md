# OAuth2.0

## Role

- resource owner
보호된 리소스에 대한 액세스 권한을 부여할 수 있는 엔티티
resource owner가 개인인 경우 엔드 유저를 뜻한다.

e.g.) 페이스북에 가입한 유저

- resource server (=리소스 서버)
보호받는 리소스들을 호스팅하는 서버이며 액세스 토큰을 사용하여 보호된 리소스 요청을 수락하고 응답한다.

e.g.) 페이스북 API 서버

- client
리소스 소유자를 대신하고 권한으로 보호된 리소스를 요청하는 응용 프로그램

e.g.) 프로덕트 서버

- authorization server (=인증 서버)
리소스 소유자를 인증하고 권한을 얻은 후 서버가 클라이언트에 액세스 토큰을 발급한다.

e.g.) 페이스북 인증 서버

인증서버는 리소스서버와 동일한 서버이거나 분리되어 있을 수 있다.
단일 인증서버는 여러 리소스서버가 허용하는 access token을 발급한다.

## 권한 부여 방법

1. Authorization Code

  ![code flow](static/oauth2_authorization_code_flow.png)

  `authorization code`는 client와 resource owner 사이에 인증서버를 중개자로 사용하여 얻는 방식
  resource owner에게 직접 인증을 요청하는 대신, client는 resource owner를 인증서버로 보내고, 
  resource owner는 권한 부여코드와 함께 client로 돌아간다.

  resource owner를 권한 부여코드와 함께 client로 되돌려 보내기 전에 인증서버는 resource owner를 인증하고 인증을 받는다.
  resource owner는 오직 인증서버로만 인증되므로 resource owner의 credential은 client와 절대 공유 되지 않는다.

  `authorization code`는 클라이언트 인증 기능 및 액세스 토큰을 리소스 소유자의 사용자 에이전트를 통해 전달하지 않고 클라이언트로 직접 전송하여 잠재적으로 리소스 소유자를 비롯한 다른 사용자에게 노출하는 등의 몇 가지 중요한 보안 이점을 제공합니다.

1. Implicit

  ![implicit flow](static/oauth2_implict_flow.png)

  `Implicit` 부여 방식은 javscript와 같은 script 언어를 통해 브라우저에 구현된 client를 위해 최적화 된 간단한 `authorization code` 방식이다.
  `Implicit` 방식에서는 client 에게 인증 코드를 발급하는 대신에, 클라이언트는 곧바로 액세스 토큰을 발급받는다.
  authorization code와 같은 발급된 중간 자격 증명이 없으므로 권한 부여 방식이 암시적이다.
  `Implicit` 방식은 액세스 토큰을 얻기 위해 필요한 과정을 줄이기 때문에 일부 client의 응답성과 효율성을 향상시킨다.

  `Implicit` 흐름에서 액세스 토큰을 발급할 때 인증서버는 client를 인증하지 않는다.
  경우에 따라 액세스 토큰을 client에게 전달하는데 사용되는 리다이렉트 uri 를 통해 클라이언트 id를 검증 할 수 있다.
  액세스 토큰은 resource owner의 사용자 에이전트에 대한 액세스 권한으로 resource owner 혹은 다른 application에 노출될 수 있다.

1. Resource Owner Password Credentials

  ![password flow](static/oauth2_password_flow.png)

  resource owner 암호 인증 정보(i.e., username and password)를 액세스 토큰을 얻기 위한 권한 부여로 직접 사용 할 수 있다.
  인증정보는 오직 그들이 resource owner와 client간에 신뢰도가 높고(client가 기기의 운영체제거나 권한이 높은 운영프로그램)와 다른 권한 부여 방식이 이용 불가능 할 때에만 사용해야한다.

  이 허가 유형에는 resource owner 자격 증명에 대해 client가 직접 접근하는 것이 필요하지만 resource owner 자격 증명은 단일 요청에 사용되고 액세스 토큰으로 교환된다.
  이 허가 유형은 자격 증명을 오래 유지되는 액세스 토큰이나 refresh token으로 바뀌기 때문에 client가 resource owner 자격 증명을 미래를 위해 저장해야 하는 필요를 제거해준다.

1. Client Credentials

  ![client flow](static/oauth2_client_credentials_flow.png)

  권한 부여 범위가 클라이언트가 제어하는 보호된 리소스 또는 권한 부여 서버에서 이전에 준비한 보호된 리소스로 제한될 때 클라이언트 인증 정보(또는 다른 형식의 클라이언트 인증)를 권한 부여로 사용할 수 있다.
  클라이언트 자격 증명은 일반적으로 클라이언트가 자체 작업을 수행하거나(클라이언트는 리소스 소유자이기도 함) 권한 부여 서버에서 이전에 할당한 권한에 따라 보호된 리소스에 대한 액세스를 요청할 때 권한 부여로 사용됩니다.

## Reference

- https://tools.ietf.org/html/rfc6749
- http://www.bubblecode.net/en/2016/01/22/understanding-oauth2/

## 작성일
- 2020-08-16
