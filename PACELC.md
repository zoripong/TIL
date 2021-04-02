# CAP 이론

어떠한 분산 시스템도 3가지를 만족시킬 수 없다.

## consistency (일관성)

"모든 요청은 최신 데이터 또는 에러를 응답받는다."

다중 클라이언트에서 같은 시간에 조회하는 데이터는 항상 동일한 데이터임을 보증한다.

느슨하게 처리되는 경우 시간의 흐름에 따라 여러 노드에 전파하는 것을 이야기한다.

분산 노드간 데이터 동기화를 위해서는 2가지 방법을 사용한다.

- 데이터 저장 결과를 클라이언트로 응답하기 전 모든 노드에 데이터를 저장하는 동기식 방법
    - 느리지만 확실한 데이터 정합성을 가지고 있다.
- 메모리나 임시 파일에 기록하고 클라이언트에 먼저 응답한 후 이벤트 또는 프로세스를 이용하여 비동기식으로 동기화
    - 쓰기 노드에 장애가 발생한 경우 손실이 있을 수 있음

## availability (가용성)

"모든 요청은 정상 응답을 받는다."

모든 클라이언트의 읽기와 쓰기 요청에 대해 항상 응답이 가능해야 한다.

이것이 잘 지켜지는 경우 일부 노드가 망가지더라도 정상적인 서비스가 가능하다.

- 복제 방법
    - 동일한 데이터를 가진 저장소를 하나 더 생성하는 master-slave
    - 데이터 단위로 중복 저장하는 peer-to-peer

## partition tolerance (네트워크 분할 허용성)

"네트워크에 문제가 있는 경우라도 시스템은 정상 동작 한다."

- 네트워크 문제가 생겨서 메시지를 주고받지 못하는 상황이라도 동작해야 한다.
- Availablity와의 차이점은 Availability는 특정 노드가 “장애”가 발생한 상황에 대한 것이고 Tolerance to network Partitions는 노드의 상태는 정상이지만 네트워크 등의 문제로 서로간의 연결이 끊어진 상황에 대한 것이다

# PACELC 이론

## CAP 이론의 논란

- P에 대한 정의가 일관성이 없다.
- C와 A는 분산시스템의 특성이지만, P는 분산시스템이 돌아가는 네트워크에 대한 특성이다.
- C와 A가 지켜지는 것이 P에 따라 달라진다.
    - HBase의 경우 네트워크 장애 상황에서는 A를 포기하고, 정상일 때는 만족한다.
- 원하든 원치 않든 P는 선택되어야 하며 결국 선택권은 C나 A중 하나밖에 없다.
    - 애초에 P는 네트워크 장애가 있을 수 있다는 것을 인정하느냐의 문제이고 이는 인정 할 수밖에 없는 문제이므로 처음부터 선택되어있는 것이다.

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d13f8be8-8ac9-4ba0-8058-5f44b7d14008/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d13f8be8-8ac9-4ba0-8058-5f44b7d14008/Untitled.png)

## PACELC 이론

- 네트워크 장애 상황과 정상 상황으로 나눠어서 설명하는 이론
    - 네트워크 파티션 상황에서는 availability, consistency 중 하나만 선택할 수 있다.
        - 가용성을 택하거나 일관성을 택하거나
    - 정상 상황에서는 latency, consistency 중 하나만 선택할 수 있다.
        - 지연 시간을 택하거나 일관성을 택하거나

## 참고

- [https://hamait.tistory.com/197](https://hamait.tistory.com/197)
- [http://eincs.com/2013/07/misleading-and-truth-of-cap-theorem/](http://eincs.com/2013/07/misleading-and-truth-of-cap-theorem/)
