# Proxy
- 목적지에 직접 접근하지 않고 데이터를 주고 받기 위해 사용하는 중간 다리 역할

## Forward Proxy
- 사용자의 Endpoint는 최종 목적지 서버
- 기기에서 프록시 설정을 하면 직접 외부 서버에 데이터 요청을 하는 것이 아니라 포워드 프록시로부터 간접적으로 원하는 서버의 데이터를 받아오게 되는 것

## Reverse Proxy
- 사용자의 Endpoint는 프록시 서버
- 보통 load balancing이나 보안상 이유로 쓰는 것이 일반적
- 리버스 프록시 뒤에 여러 개의 서버를 배치하고 사용자 요청을 분산할 때 유용하게 사용됨
- eg. nginx, apache