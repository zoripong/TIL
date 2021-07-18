# Synchronous vs Asynchronous

## Synchronous

- 두 가지 이상의 대상이 서로 시간을 맞춰 행동하는 것
- 함수 요청 후 처리가 완료될 때까지 **기다렸다가** 응답을 받는 것

## Asynchronous

- 두 가지 이상의 대상이 서로 시간을 맞추지 않고 행동하는 것
- 요청 후 다른 일을 하다가 처리가 **완료되면** 응답을 받는 것

# Blocking vs Non-Blocking

- synchronous와 asynchronous와 관점이 다르다.
- blocking, non-blocking은 직접 제어할 수 없는 대상을 처리하는 방법에 따라 나눈다.

## Blocking I/O Model

![blocking.png](/static/TIL/backend/blocking.png)

- I/O 작업은 application 레벨에서 수행할 수 없고 Kernel Level (OS)에서 일어난다.
    - application은 커널에게 I/O 작업을 요청해야 한다. (System Call)
- Application이 Block되어 다른 작업 수행이 불가능 (자원 낭비)
- Block이 안 된 듯 보이는 이유는, Multi Thread 기반으로 동작
    - Block 되는 순간 다른 Thread가 동작함
    - Thread 간의 전환 (Context Switching)에 드는 비용이 듬

# Non-Blocking I/O Model

- 요청 후 응답까지 다른 작업을 함
- synchronous non-blocking
    - 특정시간마다 주기적으로 완료되었는지 상태를 확인 (polling)
    - 불필요한 자원 낭비 발생
- asynchronous non-blocking
    - 응답이 완료되면 이벤트를 발생시켜 알려줌
    - 미리 등록해둔 callback을 통해 이후 작업 진행
