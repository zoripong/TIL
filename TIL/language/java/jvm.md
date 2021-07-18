
# JVM

- Java Virtual Machine
- Java Byte Code를 OS에 맞게 해석해주는 역할을 한다.
- Java Compiler는 java파일을 class라는 java byte로 변환해주지만, byte code는 기계어가 아니기 때문에 OS에서 바로 실행이 되지 않는다.
    - 이때, JVM이 OS가 Byte code를 이해할 수 있도록 해석해준다.
- JVM의 해석을 거치기 때문에 c와 같은 네이티브 언어에 비해 느리다.
    - JIT을 이용하여 속도를 개선했다.
    - 프로그램을 실행하는 시점에 필요한 부분을 즉석으로 컴파일 하는 방식이다.
    - JIT 컴파일러는 같은 코드를 매번 해석하는 대신 처음 실행될 때 인터프리트를 하면서 자주 사용되는 코드를 캐싱한 뒤, 이후에는 캐싱된 것을 가져와 사용하기 때문에 인터프리터보다 빠르다.
- byte code는 JVM 위에서 운영체제와 관계없이 실행되는 것이 최대 장점이다. 


## JVM 구조
- Class Loader
  - Runtime 시점에 class를 로딩하게 해주며 클래스의 인스턴스를 생성하면 클래스 로더를 통해 메모리에 로드하게 된다.

- Runtime Data Areas
  - JVM이 프로그램을 수행하기 위해 OS로부터 별도의 할당 받은 메모리 공간
  - 크게 5가지 영역으로 나눌 수 있다.
    - PC Register
    - Java Vitrual Machine Stack
    - Native Method Stack
    - Method Area
    - Heap

- Execution Engine

## 자바 프로그램 실행 과정

[JVM](/static/TIL/language/java/jvm.png)


1. JVM은 OS로부터 프로그램이 필요로 하는 메모리를 할당 받는다.
    - JVM은 이 메모리를 용도에 맞게 여러 영역으로 나누어 관리한다.
1. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.calss)로 변환시킨다.
1. Class Loader 통해 class파일들을 JVM으로 로딩한다.
1. 로딩된 class 파일들은 Execution Engine을 통해 해석된다.
1. 해석된 바이트코드(.class)는 Runtime Data Areas에 배치되고 수행이 이루어진다.
    - 이 과정에서 JVM은 필요에 따라 GC와 같은 관리 작업을 수행한다.
