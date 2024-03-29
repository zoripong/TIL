# Backoff 알고리즘

- 통신할 때 충돌이 일어난 경우 일정시간동안 기다린 후 다시 호출 하는 방식
- 보통 Timeout 상황은 특정 시간동안 네트워크 이슈가 지속될 것이기 때문에 단순 3회 재시도는 실패할 가능성이 높을 뿐만 아니라 문제가 발생한 네트워크에 더 부담을 줄 가능성이 크다.
    - 트래픽이 몰려서 요청 자체가 지연되고 있는데 모든 클라이언트가 재시도를 연속으로 시도하면 해당 서버는 회복하더라도, 이 요청에 의해 다시 죽을 수 있다.

## Expotential Backoff

- 점진적으로 시간 간격을 늘이며 요청하는 전략
- 갑작스러운 트래픽 부담을 방지할 수 있다.
- 그러나 해당 방법을 사용해도 동시에 요청이 몰린다면 똑같은 시간 간격으로 재시도를 하며 재시도가 다시 몰릴 것이기 때문에 한계가 있다.

## Jitter(지연변이)

- Jitter는 데이터 통신 용어로 사용될 때에는 패킷 지연이 일정하지 않고, 수시로 변하면서 그 간격이 일정하지 않은 현상이다.
- Jitter개념을 Expotential Backoff에 적용하여 Retry하는 시간 간격을 조금씩 변이 시켜 요청하도록한다.
- 지수로 증가하는 Backoff 시간에 일정 범위 안의 랜덤 대기 시간을 추가적으로 더하는 방식이다.
- 랜덤한 값에 의해 재시도가 모두 다른 시점에 될 것이기 때문에 부하를 분산시킬 수 있다.

# 참고
[https://jungseob86.tistory.com/12](https://jungseob86.tistory.com/12)
