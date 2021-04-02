# HTTP/0.9

- 최초 HTTP 프로토콜
- 0.9 라는 버전도 추후에 구별하기 위해 붙어진 번호
- 요청은 오직 리소스의 경로와 `GET`으로 이루어져 있었음

    ```jsx
    GET /mypage.html
    ```

- 응답은 오로지 파일 내용으로만 구성
    - 오로지 HTML 파일만 전송 가능
    - 오류 코드 또한 없었음
        - 문제 발생시 HTML 파일 내부에 문제에 대한 설명과 함께 응답

# HTTP/1.0

- 프로토콜 버전 정보가 요청 시작 부분에 붙기 시작
- 상태 코드 또한 응답 시작에 붙기 시작
    - 브라우저가 요청 결과를 알 수 있고 그 결과에 따라 동작할 수 있게 됨
- HTTP 헤더가 요청과 응답에 모두 도입 됨
    - 덕분에 HTML 파일이 아닌 경우도 주고 받을 수 있게 됨 (Content-Type)
- 요청/응답 예시
    ```jsx
    GET /mypage.html HTTP/1.0
    User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

    200 OK
    Date: Tue, 15 Nov 1994 08:12:31 GMT
    Server: CERN/3.0 libwww/2.17
    Content-Type: text/html
    <HTML>
    A page with an image
      <IMG SRC="/myimage.gif">
    </HTML>
    ```

# HTTP/1.1

- 첫번째 표준
- 텍스트 프로토콜
- 커넥션이 재사용될 수 있게 함
- 파이프라이닝 추가
    - 첫번째 요청에 대한 응답이 완전히 전송되기 전 두번째 요청 전송을 가능하게 함
- 청크된 응답 지원
- 추가적인 캐시 메커니즘 도입
- 언어, 인코딩 혹은 타입을 포함한 컨텐츠 협상이 도입
    - 클라이언트와 서버로 하여금 교환하려는 가장 적합한 컨텐츠에 대한 동의를 가능케함
- `Host` 헤더를 통해 동일 IP 주소에 다른 도메인을 호스트하는 기능이 가능해짐
- 요청/응답 예시
    ```jsx
    GET /en-US/docs/Glossary/Simple_header HTTP/1.1
    Host: developer.mozilla.org
    User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate, br
    Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header

    200 OK
    Connection: Keep-Alive
    Content-Encoding: gzip
    Content-Type: text/html; charset=utf-8
    Date: Wed, 20 Jul 2016 10:55:30 GMT
    Etag: "547fa7e369ef56031dd3bff2ace9fc0832eb251a"
    Keep-Alive: timeout=5, max=1000
    Last-Modified: Tue, 19 Jul 2016 00:59:33 GMT
    Server: Apache
    Transfer-Encoding: chunked
    Vary: Cookie, Accept-Encoding

    (content)
    ```

# HTTP/2.0

- 구글에서 만든 비표준 개방형 네트워크프토콜인 **SPDY** 프로토콜이 기초가 됨
- 이진 프로토콜
    - 읽을 수도, 직접 만들 수도 없음
- 병렬 요청이 동일한 커넥션 상에서 다루어질 수 있는 다중화 프로토콜
    - 핸드쉐이크 과정을 줄임

![http 2.0](static/http_2_0.png)

- **전송된 데이터의 분명한 중복과 그로 인한 오버헤드를 제거하여 연속된 요청 사이의 유사한 내용의 헤더를 압축시킴**
- 사전에 클라이언트 캐시를 서버 푸쉬라고 불리는 메커니즘에 의해 필요하게 될 데이터로 채워넣을 수 있도록 함
- 클라이언트 요청이 없어도 서버가 리소스를 보낼 수 있음.
    - 클라이언트 요청을 줄일 수 있기 때문에 성능 향상에 도움이 됨

# HTTP/3.0

- 최초로 UDP 기반의 QUIC을 사용하여 통신하는 프로토콜

## 기존 TCP 기반의 문제
- 핸드셰이크 하는 과정이 번거로움
- HOLB (Head of line Blocking)
    - TCP 통신에서는 패킷의 순서가 무조건 정확한 순서대로 처리되어야 함
        - 수신 측은 송신 측과 주고받은 시퀀스 번호를 참고하여 패킷을 재조립하기 때문
    - 패킷이 중간에 유실되거나 수신 측 패킷 파싱 속도가 느리면 병목이 생기게 되는 현상

## UDP를 선택한 이유
- 핸드쉐이크 과정이 필요없다.
    - UDP는 데이터그램 방식을 이용하는 프로토콜이기 때문에 각 패킷간의 순서가 존재하지 않는 독립적인 패킷을 이용
    - 목적지만 정해져 있다면 중간 경로는 상관없다.
- 커스터마이징이 가능하다.
    - TCP에 비해 헤더가 거의 비어있다.
        - 출발지, 도착지, 패킷 길이, 체크섬 밖에 없다.
    - UDP 프로토콜 자체는 TCP보다 신뢰성이 낮고 흐름제어가 안되지만 개발자가 구현을 어떻게 하느냐에 따라 TCP와 비슷한 수준을 가질 수 있다.

## 개선된 점
- 연결 설정 시 레이턴시 감소
    - 3 Way Handshake 과정을 거치지 않아도 된다.
    - RTT가 TLS까지 합해지면 3 RTT가 필요해진다.
    - RTT: Round Trip Time, 클라이언트가 보낸 요청을 서버가 처리한 후 다시 클라이언트로 응답해주는 사이클
    - 반면 QUIC는 첫 연결 설정에 1 RTT만 소요된다.
    - 첫 핸드쉐이크시 연결 설정에 필요한 정보와 함께 데이터도 보내버린다.
    - 한 번 연결 성공 후에는 세션 정보를 캐싱해두어 0 RTT만으로 통신한다.
- 패킷 손실 감지에 걸리는 시간 단축
    - QUIC도 TCP와 마찬가지로 ARQ 방식을 사용하는 프로토콜
        - ARQ: 에러 발생시 재전송을 통해 에러를 복구하는 방식
    - TCP는 Stop and Wait ARQ 방식을 이용하여, 송신 측이 패킷을 보낸 후 일정 시간이 경과해도 수신 측이 적절한 답변을 주지 못하면 다시 패킷을 보낸다.
    - QUIC은 TCP와 유사하지만 몇 가지를 보완하였다.
        - 재전송 모호성을 보완하기 위해 헤더에 별도의 패킷 번호 공간을 부여
            - 재전송 모호성: 타임아웃 시간 계산을 동적으로 해야하는데, 타임아웃이 발생해서 패킷 손실이 발생하면 애매해진다. 
                - 패킷 전송 -> 타임 아웃 -> 패킷 재전송 -> ACK 받음! (근데 이거 첫 번째로 보낸 패킷의 ACK야? 두 번째로 보낸 패킷의 ACK야?)
        - 패킷의 전송 순서 자체만을 나타내며, 매 전송마다 모노토닉하게 패킷 번호가 증가한다.
- 멀티플렉싱 지원
    - 단일 연결 안에서 여러가지 데이터를 섞이지 않게 보내는 기법
    - HOLB를 방지해준다.
    - 하나의 스트림에 문제가 발생해도 다른 스트림은 지킬 수 있다.
- 클라이언트 IP가 바뀌어도 연결이 유지됨
    - TCP의 경우 소스의 IP주소와 포트, 연결 대상의 IP주소와 포트로 연결을 식별하여 IP가 변경되면 연결이 끊어짐
        - 이 경우 다시 연결을 맺기 위해 3 Way handshake 절차를 밟아야 함
    - QUIC은 connection ID를 사용하여 서버와 연결을 생성하여 클라이언트 IP가 변경되더라도 기존 연결을 유지할 수 있다.

## 참고자료
- https://evan-moon.github.io/2019/10/08/what-is-http3/
