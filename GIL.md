# GIL (Global Interpreter Lock)

- 운영체제가 생성하는 작업 단위를 process라고 한다. 이 process 안에서 공유되는 메모리를 바탕으로 여러 작업을 또 생성할 수 있는데, 이 때의 작업 단위를 thread라고 한다. 따라서 각 thread 마다 할당된 개인적인 메모리가 있으면서, thread가 속한 process가 가지는 메모리에도 접근할 수 있다.
- 여러 thread가 공유된 데이터를 변경함으로써 발생하는 문제를 race condition이라고도 부른다.
- Thread-safe한 코드를 만들기 위해서 사용하는 것 중 하나가 mutex (mutual exclusion)이다. 위에서 본 참사를 막기 위해서, 공유되는 메모리의 데이터를 여러 thread가 동시에 사용할 수 없도록 잠그는 일을 mutex가 맡는다.
- CPython이 GC를 위해 reference counting을 하는 중간에 race condition이 발생하게 되면 메모리에 문제가 생긴다.
    - 아직 해제되면 안 되는 변수가 해제되거나
    - 해제되어야 하는데 해제 되지 않아 메모리 누수가 생기거나
- 위 문제를 방지하기 위해 reference counting을 할 때에는 mutex를 lock해야 한다.
    - 따라서 오브젝트 하나하나 할당 할 때마다 lock을 걸어야 한다.
    - 그러나 이것은 성능적으로 매우 좋지 않고, 데드락이 발생할 확률이 크다.
- 그래서 파이썬은 인터프리터 자체를 Lock 시키기로 하였다.
    - 인터프리터만 lock하면 문제가 해결된다.
- 그러나 인터프리터가 lock 되면서 thread와 상관없이 오직 하나의 코드만 실행될 수 있게 되었다.
- 파이썬에 사람이 몰려들고 여러 C extension들이 만들어지고 난 후 thread의 문제가 대두되기 시작했다. 메모리 관리 방법 변경을 위해 모든 C extension을 고치기보다 인터프리터를 lock하는 것이 더 현실적이라고 판단했다고 한다.
- 동시성 처리에 대해서 굳이 멀티쓰레드가 아니더라도, 멀티프로세싱, asyncio 등 대안이 있다.
