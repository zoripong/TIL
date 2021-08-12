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


- Mono -> Flux: flatMapMany 연산자
- Flux -> Mono: reduce

- 값을 꺼내서 Publisher로 바꿔줄 수 있는 연산자
  - flatMap (리턴하는 Publisher가 비동기로 동작할 때 순서를 보장하지 않음)
  - flatMapSequential (오는대로 구독하고 결과는 순서에 맞게 리턴)
  - concatMap (인자로 지정된 함수에서 리턴하는 Publisher의 스트림이 다 끝난 후에 그 다음 넘어오는 값의 Publisher 스트림을 처리)


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

# 시퀀스 변환

## map
- 한개의 데이터를 1:1 방식으로 변환시켜준다.
- 동기 function을 적용하여 Mono에 의해 emit된 값을 변형

## flatMap
- 1개의 데이터로부터 시퀀스를 생성할 때 사용한다. (1:N)
- flatMap에 전달한 함수가 생성하는 각 Flux는 하나의 시퀀스로 연결된다.
  - flatMap에서 Flux<Integer>를 반환해도 결과값은 Flux<Flux<Integer>>가 아닌 Flux<Integer>이다.
- Mono에 의해 비동기성으로 emit된 아이템을 다른 Mono 형태로 emit된 값으로 변형하여 반환한다.

## filter
- 시퀀스가 생성한 데이터를 걸러낼 수 있다.
- filter에 전달한 함수 결과가 true인 데이터만 전달한다.

## defaultIfEmpty
- 시퀀스에 데이터가 없을 때 특정 **값**을 기본으로 사용하고자 할 때 사용한다.

## swtichIfEmpty
- 시퀀스에 값이 없을 때 다른 **시퀀스**를 사용하고자 할 때 사용한다.
- defaultIfEmpty는 값을 지정하지만 switchIfEmpty는 시퀀스를 지정한다.

## startWith
- 시퀀스의 시작값을 지정해줄 수 있다.
- 지정된 값이 시퀀스 앞에 붙게 된다.

## concatWithValues
- 시퀀스가 특정 값으로 끝나도록 지정할 수 있다.

## concatWith
- 여러 시퀀스를 연결할 수 있다.
- 앞의 시퀀스가 끝나야 두번째로 연결된 시퀀스의 구독이 시작된다.
  - seq1.concatWith(seq2).subscribe(System.out::Println) 인 경우 seq1이 종료된 후 seq2를 구독한다.

## mergeWith
- 여러 시퀀스를 연결할 수 있다.
- concatWith는 시퀀스를 연결한 순서대로 구독하지만, mergeWith는 발생하는 데이터 순서대로 구독하게 된다.

## zipWith

[zipWith](/static/TIL/backend/spring/zipwith.png)

- 두 시퀀스의 값을 묶어 하나의 쌍을 생성하는 시퀀스를 만들 수 있다.

## combineLatest

[combineLatest](/static/TIL/backend/spring/combine_latest.png)

- 가장 최근 데이터를 기준으로 쌍을 만든다.

## take

- 지정한 개수만큼 유지할 때 사용한다.
- Duration을 인자로 넘겨주는 경우 해당 시간동안 생성된 데이터를 유지해준다.

## skip

- 지정한 개수만큼 거르고 싶을 때 사용한다.
- Duration을 인자로 넘겨주면 해당 시간동안 생성된 데이터를 걸러준다.
