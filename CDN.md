# CDN (Content Delivery Network)
- 지리적 제약 없이 전 세계 사용자에게 빠르고 안전하게 콘텐츠를 전송할 수 있는 콘텐츠 전송 기술
  - HTML 페이지, javascript 파일, style sheet, 이미지 및 비디오 등
- CDN은 서버와 사용자 사이의 물리적인 거리를 줄여 콘텐츠 로딩에 소요되는 시간을 최소화
- 각 지역에 캐시 서버(PoP, Points of presence)를 분산 배치해, 근접한 사용자의 요청에 원본 서버가 아닌 캐시 서버가 콘텐츠를 전달
- DDOS (Distributed Denial of Service) 공격과 같은 일부 악의적인 공격으로부터 웹 사이트를 보호하는 데 도움

## 데이터 전송 과정
1. 클라이언트로부터 Edge Server로의 요청이 발생한다.
1. Edge Server는 요청이 발생한 데이터에 대하여 캐싱 여부를 확인합니다.
1. 사용자의 근거리에 위치한 Edge Server 중 캐싱 데이터가 존재한다면 사용자의 요청에 맞는 데이터를 응답합니다.
1. 사용자의 요청에 적합한 데이터가 캐싱되어 있지 않은 경우 Origin Server로 요청이 포워딩됩니다.
1. 요청받은 데이터에 대해 Origin Server에서 획득한 후  Edge Server에 캐싱 데이터를 생성하고, 클라이언트로 응답이 발생합니다.

## 장점
- 웹 사이트로드 시간 개선
  - 웹 사이트 방문자에게 더 가까운 컨텐츠를 배포함으로써 방문자는 페이지로드 시간이 더 빨라짐
- 대역폭 비용 절감
- 콘텐츠 가용성 및 이중화 증가
  - 분산되어 있어 많은 트래픽을 처리할 수 있음

## 참고
https://velog.io/@leyuri/CDN
https://library.gabia.com/contents/infrahosting/8985/