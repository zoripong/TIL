
# Spring!

- 크게 reactive stack과 servelet stack으로 나뉘어짐

## Spring MVC?

![spring_mvc.png](/static/TIL/backend/spring/spring_mvc.png)

- servelet stack
- 다수의 요청이 들어오는 경우 Queue에 쌓이고 Thread Pool의 Thread들이 가져가 처리
- Thread Pool의 size보다 많은 요청이 들어오게 되면 Queue가 쌓이게 됨 (Thread Pool Hell)
- 트래픽을 측정하여 Pool size를 조정해야 함

## Spring Webflux?

![event_loop.png](/static/TIL/backend/spring/event_loop.png)

- Event driven 방식
- Reactor: JVM 위에서 동작하는 논블럭킹 애플리케이션을 만들기 위한 리액티브 라이브러리
- Node.js처럼 이벤트 루프가 돌고 요청이 발생한 경우 그것에 맞는 핸들러에게 처리를 위임하고 처리가 완료되면 callback 메소드 등을 통해 응답을 반환 (non-blocking)
- 사용자 요청을 대량으로 받아낼 수 있음
- 요청을 처리하는 파이프라인의 요소들이 **모두** 논블로킹하게 동작해야 유의미
    - 특정 구간에서 블로킹이 발생한다면 MVC와 동일한 문제들이 발생
    - R2DBC 를 이용하여 non-blocking 으로 DB connnection 을 만들 수 있음
- 지원하는 프로그래밍 모델
    - 어노테이션 기반 리액티브 컴포넌트
        - SpringMVC와 유사한 방식으로 구현
            - RequestMapping, PathVariable, RestController 등
    - 함수형 라우터와 핸들러
        - 라우터 함수
            - RequestPredicate을 통해 클라이언트 request를 관리하는 라우터
        - 핸들러 함수
            - 라우터 함수로 틀어온 reqeust에 대한 처리 정의


### Event-Driven

- 프로그램 실행 흐름이 이벤트에 의해 결정되는 프로그래밍 패러다임
    - ex. EventLoop는 예—전에 콘솔 만들때 방향키 입력 감지하던 무한루프 반복문
- Event가 발생할 때 이를 감지하고 적합한 이벤트 핸들러를 사용하여 처리
- 언어 레벨에서 Event Loop를 돌면서 요청을 감지하고 적합한 Handler에게 위임
    - 개발자는 단순히 Listener 액션만 등록해주면 된다.


### Reactor

#### Mono와 Flux
- Mono: 0-1개의 결과를 처리하기 위한 Reactor 객체
- Flux: 0-N개의 결과를 처리하기 위한 Reactor 객체
- Mono -> Flux: flatMapMany 연산자
- Flux -> Mono: reduce

- 값을 꺼내서 Publisher로 바꿔줄 수 있는 연산자
  - flatMap (리턴하는 Publisher가 비동기로 동작할 때 순서를 보장하지 않음)
  - flatMapSequential (오는대로 구독하고 결과는 순서에 맞게 리턴)
  - concatMap (인자로 지정된 함수에서 리턴하는 Publisher의 스트림이 다 끝난 후에 그 다음 넘어오는 값의 Publisher 스트림을 처리)

#### Map vs FlatMap

- Map
  - 동기 function을 적용하여 Mono에 의해 emit된 값을 변형
- FlatMap
  - Mono에 의해 비동기성으로 emit된 아이템을 다른 Mono 형태로 emit된 값으로 변형하여 반환한다.



## MVC vs WebFlux

![MVC_vs_Webflux.png](/static/TIL/backend/spring/MVC_vs_Webflux.png)

boot1 - MVC / boot2 - WebFlux

- 사용자가 적을 때에는 유의미한 차이는 발생하지 않음
- 동기방식이 코드작성, 이해, 디버깅이 더 쉽다고 함

# Ref

- [https://www.popit.kr/spring-webflux와-kotlin으로-만드는-todo-서비스-1편/](https://www.popit.kr/spring-webflux%EC%99%80-kotlin%EC%9C%BC%EB%A1%9C-%EB%A7%8C%EB%93%9C%EB%8A%94-todo-%EC%84%9C%EB%B9%84%EC%8A%A4-1%ED%8E%B8/)
- [https://velog.io/@dyllis/Spring-MVC-vs-WebFlux](https://velog.io/@dyllis/Spring-MVC-vs-WebFlux) 👍
- [https://devahea.github.io/2019/04/21/Spring-WebFlux는-어떻게-적은-리소스로-많은-트래픽을-감당할까/](https://devahea.github.io/2019/04/21/Spring-WebFlux%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%A0%81%EC%9D%80-%EB%A6%AC%EC%86%8C%EC%8A%A4%EB%A1%9C-%EB%A7%8E%EC%9D%80-%ED%8A%B8%EB%9E%98%ED%94%BD%EC%9D%84-%EA%B0%90%EB%8B%B9%ED%95%A0%EA%B9%8C/) 👍
