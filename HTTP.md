# CORS

## 필요한 이유

만약 내가 서비스하고 있지 않은 사이트에서 세션을 요청해서 세션을 획득할 수 있다면 해당 사이트는 악의적으로 내 세션을 탈취하거나 다른 행동을 할 수 있기 때문에 이런 요청을 막아야 함

허용한 origin들만 요청할 수 있도록 하기 위해 CORS가 필요함

## 동작 방식

- 브라우저에서 리소스 요청시 추가적인 헤더에 origin과 관련된 정보를 담음
- 서버는 응답할 수 있는 origin을헤더에 담아서 브라우저에게 반환
- 브라우저는 이 헤더를 비교하여 요청할 수 있다면 리소스 전송을 허용하고 불가능하다면 에러를 발생

# 주소창에 URL

- DNS
- ARP
- 3way handshake
- TLS handshake

# OSI 7계층

- 네트워크에서 통신이 일어나는 과정을 7단계로 나눈 것
- 계층을 나눈 이유는 통신이 일어나는 과정이 단계별로 파악할 수 있도록 하기 위함

# UDP vs TCP

![udp vs tcp](static/udp_vs_tcp.png)

# HTTP 버전별 차이

[HTTP 버전별 차이](HTTPVersion.md)

# RESTful API

## REST 구성

자원 / 행동 / 표현

## 특징

### 유니폼 인터페이스

URI로 지정한 리소스에 대한 조직을 통일되고 한정적인 인터페이스로 수행하는 아키텍쳐 스타일

### 무상태성

상태정보를 따로 저장하거나 관리하지 않는다.

### 캐시 가능

REST는기본적으로 HTTP 웹표준을 따라가며, HTTP가가진 캐싱 기능 적용이 가능하다.

### 자체 표현 구조

API 메시지만 보고도 쉽게 이해할 수 있는 구조로 되어있다.

### Client - Server 구조

서버는 API제공, 클라이언트는 사용자 인증 및 컨텍스트 관리 구조로 각 역할이 확실하게 구분되어 책임이 명확해지고 의존성이 줄어듬

### 계층형 구조

- 다중 계층 구성 가능

# 디자인 가이드

1. URI는 정보의 자원을 표현해야 한다.
2. 자원에 대한 행위는 http method로표현한다.

### Method

GET - 조회

POST - 생성

PUT - 수정

DELETE - 삭제

## Response

200 - ok

201 - created

301 - permanently redirect

400 - bad request

401 - unauthorized

403 - forbidden

404 - not found

500 - internal server error

501 - not implemented

502 - bad gateway

503 - service unavailable

504 - gateway timeout