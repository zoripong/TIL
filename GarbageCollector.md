# Garbage
- 정리되지 않은 메모리
- 유효하지 않은 메모리

# Garbage Collector
- 메모리가 부족할 때 이런 Garbage들을 메모리에서 해제 시켜 다른 용도로 사용할 수 있도록 함

## Stop The World
- GC 실행을 위해 애플리케이션이 실행을 멈추는 것
- GC가 실행 될 때에는 GC 실행 스레드 외에 모든 스레드가 작업을 멈춘다.

## 과정 (Mark and Sweep)
- Mark, 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각 어떤 객체를 참고하고 있는지 찾는다.
  - 해당 과정에서 Stop The World가 발생
- Sweep, 이후 Mark 되어있지 않은 객체들을 힙에서 제거

## Minor GC와 Major GC
- JVM의 Heap은 young, old, perm 영역으로 나뉜다.
- young 영역에서의 GC를 minor GC, 나머지 영역에서의 GC를 major GC (full GC)라고 부른다.

### young 영역
- 새롭게 생성한 객체가 위치
- 대부분 객체가 금방 unreachable 상태가 되기 때문에 많은 객체가 young 영역에 생성되었다가 사라짐

### old 영역
- young 영역에서 reachable 상태를 유지해 살아남은 객체가 복사되는 영역
- 대부분 young 영역 보다 크게 할당하며 크기가 큰 만큼 비교적 GC가 적게 발생

### perm 영역
- method area
- 클래스와 메소드 정보와 같이 자바 언어레벨에서는 거의 사용되지 않는 영역

## Reachability
- Java GC는 가비지 객체를 판별하기 위해 reachability 개념을 사용
- 어떤 객체에 유효한 참조가 있으면 'reachable', 없으면 'unreachable'로 구분하고 'unreachable' 객체를 가비지로 간주
- 객체에 대한 reachability 를 제어할 수 있으면 코드를 통해 Java GC에 일부 관여 할 수 있음
- 캐시를 만들려고 할 때 캐시 내부 데이터는 의미가 없어진 상황에서도 Strong Reference로 가비지로 인식되지 않을 수 있다.

## 참고
https://velog.io/@litien/%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%ED%84%B0GC#minor-gc-%EC%99%80-major-gc
