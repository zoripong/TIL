# Companion Object
## static

- 코틀린에는 static이 없다.
- java에서는 block, variables, method, nested class에 static 키워드를 이용할 수 있었다.
- static 키워드가 붙으면 인스턴스가 실행될 때마다 실행되는 것이 아니라 아래 상황에서 불린다.
    - 해당 block이 선언된 클래스의 첫 번째 객체를 만들 때
    - 해당 클래스의 static memeber에 처음으로 접근할 때 (인스턴스 생성 여부와 무관)

## Object

- 싱글턴을 선언하는 방법


## Companion Object (동반자 객체)

- 클래스 내부에 객체를 선언할 때 companion 식별자를 붙인 object를 선언하면 된다.
- 클래스의 모든 인스턴스가 공유하는 객체를 만들 때 사용
- 클래스당 한 개만 가질 수 있음

![companion_object.png](/static/TIL/language/companion_object.png)

- static으로 선언된 변수처럼 보이지만 런타임에 실제 인스턴스로 실행된다.
    - 인스턴스를 구현할 수 있다.
