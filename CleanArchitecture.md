# 클린아키텍쳐?

- 계층을 분리하여 관심사의 분리하는 것
- 아키텍처가 동작하기 위해서는 의존성 규칙을 지켜야 한다
- 의존성 규칙은 모든 소스코드의 의존성은 반드시 외부에서 내부로 향해야 한다.
    - 비즈니스 코드는 DB나 Webserver framework 같은 구체적인 세부 사항에 의존하지 않아야 한다.

## 중복의 종류

### 진짜 중복

한 인스턴스가 변경되면, 동일한 변경을 그 인스턴스의 모든 복사본에 반드시 적용해야한다.

### 거짓된 중복

중복으로 보이는 두 코드의 영역이 각자의 경로로 발전한다면, 즉 서로 다른 속도와 다른 이유로 변경된다면 이 두 코드는 진짜 중복이 아니다.

## uncle-bob

> The outer circles are mechanisms. The inner circles are policies.

바깥 원은 메카니즘이고, 안에 있는 원은 규칙이다.

> There’s no rule that says you must always have just these four. However, The Dependency Rule always applies. Source code dependencies always point inwards.

이 네가지 레이어로 이루어져야 한다는 규칙은 없다. 그러나 Dependency Rule은 항상 적용되어야 한다.

코드의 의존성의 방향은 항상 안쪽으로 향해야 한다.

> We usually resolve this apparent contradiction by using the Dependency Inversion Principle.

우리는 이 명백한 모순을 항상 DIP를 이용해서 해결해왔다.

> For example, consider that the use case needs to call the presenter. However, this call must not be direct because that would violate The Dependency Rule: No name in an outer circle can be mentioned by an inner circle. So we have the use case call an interface (Shown here as Use Case Output Port) in the inner circle, and have the presenter in the outer circle implement it.

예를 들어, 프리젠터를 호출할 필요가 있는 유즈케이스가 있다. 그러나 이 호출은 바깥원에 있는 자원은 내부 안에 의해서 언급될 수 없는 Dependency Rule에 위반되기 때문에 직접 부를 수 없다. 그래서 우리는 use case는 내부 안에 있는 interface를 호출하고, 바깥 원에 있는 프리젠터는 그것을 구현하도록 한다.

## 참고자료

- [http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [https://woowabros.github.io/tools/2019/10/02/clean-architecture-experience.html](https://woowabros.github.io/tools/2019/10/02/clean-architecture-experience.html)
