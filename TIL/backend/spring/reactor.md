# Reactive Streams
- [Reactive Streams](http://www.reactive-streams.org/)는 비동기 스트림 처리를 위한 표준
- 스프링 리액터는 이를 구현한 라이브러리

- 스트림은 시간이 지남에 따라 생성되는 일련의 데이터/이벤트/신호다.
  - 컨텍스트에 따라서 데이터/이벤트/신호 중 하나를 사용한다.
  - reactive streams에서는 신호(signal)를 사용한다.

- reactive streams 는 3가지 신호를 발생할 수 있다.
  - onNext
    - 스트림은 0개 이상의 next 신호를 발생할 수 있다.
    - next 신호는 데이터를 담는다.
  - onComplete
    - 스트림이 끝났음을 의미한다.
  - onError
    - 에러가 발생하였음을 의미한다.
- onComplete와 onError는 둘 중 하나만 발생할 수 있으며, 두 신호는 발생하지 않을 수 있다.
  - 예를 들어 현재 기온을 가져오는 스트림은 1분 간격으로 next 신호만 발생시키고, onComplete나 onError는 발생시키지 않는다.
  - 반대로 파일을 읽어오는 스트림은 파일 데이터를 담은 next 신호를 발생하고 모두 읽으면 onComplete 신호를 발생한다.

- Publisher를 이용해서 스트림을 정의하고 Subscriber를 통해 신호를 처리한다.

```java
Flux<Integer> seq = Flux.just(1, 2, 3);
seq.subscribe(value -> System.out.println(value)); // Subscribe Flux
```

# Publisher - Flux and Mono

- 스프링 리액터는 flux, mono 2가지의 publisher를 가지고 있다.
  - flux는 0개 이상의 데이터를 발생시킬 수 있고,
  - mono는 0개 혹은 1개의 데이터를 발생시킬 수 있다.

- Flux.just(1, 2, 3)는 ( --1-2-3-|--> )와 같은 시퀀스를 생성한다.
- Flux.just()는 ( --|--> )와 같은 시퀀스를 생성한다.
- Mono.just(1)는 ( --1-|--> )와 같은 시퀀스를 생성한다.
- Mono.empty()는 ( --|--> )와 같은 시퀀스를 생성한다.


# Publish and Subscribe

## Cold Sequence
- cold 시퀀스는 바로 신호를 발생하지 않고, 구독을 하는 시점에 신호를 발생하기 시작한다.
- 각 구독마다 데이터를 새롭게 생성한다.
- 외부 API 요청시 구독마다 새로운 요청을 할 수 있도록 하기 위해 사용할 수 있다.

```java
Flux<Integer> seq = Flux.just(1, 2)
  .doOnNext(i -> System.out.println("doOnNext: " + i));

System.out.println("시퀀스 생성");
seq.subscribe(i -> System.out.println("Received1: " + i));
seq.subscribe(i -> System.out.println("Received2: " + i)); 
```

위 코드에 대한 실행결과는 아래와 같다.
doOnNext가 실행된 시점이 아닌 subscribe가 실행된 시점에 next 신호가 발생된다.

```
시퀀스 생성
doOnNext: 1
Received1: 1
doOnNext: 2
Received1: 2
doOnNext: 1
Received2: 1
doOnNext: 2
Received2: 2
```

## Hot Sequence
- 구독 여부에 상관없이 데이터가 생성된다.
- 구독을 하게 되면 구독 이후에 발생하는 데이터부터 신호를 받게 된다.
- 센서 데이터등에 사용할 수 있다.


## Subscriber
- subscriber는 아래 함수를 재정의 해줘야 한다.
  - onSubscribe(Subscription s): subscription을 통해 publisher에 데이터를 요
청할 수 있다. (Subscription#request())
  - onNext(T t)
  - onError(Throwable t)
  - onComplete()
- Subscription#request()를 통해 publisher에 데이터를 요청하지 않으면, next 신호가 발생하지 않는다.
- request(Long.MAX_VALUE)를 사용하면 개수 제한 없이 publisher에 데이터를 요청하는 신호를 보낸다.

https://javacan.tistory.com/entry/Reactor-Start-2-RS-just-generate