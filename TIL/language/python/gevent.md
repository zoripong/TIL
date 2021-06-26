## gevent

> Gevent is a library based on non-blocking IO (libevent/libev) and lightweight greenlets (essentially Python coroutines). Non-blocking IO means requests waiting for network IO won’t block other requests;

libev 기반 동시성 라이브러리, 동시성과 네트워크 관련 작업을 위한 다양한 API 제공한다.

## greentlet

- gevent에서 사용되는 주된 패턴
- C 확장 모듈 형태로 제공되는 경량 코루틴
- Greenlet들은 메인 프로그램을 실행하는 OS process 안에서 모두 실행되지만, 상호작용하며 스케줄링 된다.
- **한 번에 오직 하나의 greenlet 만이 실행된다.**
    - 실제로 병렬처리 되는 운영체제가 스케줄링하는 process들과 POSIX 스레드들을 사용한 multiprocessing, threading과는 다르다.
- synchronouse 스타일의 파이썬 기본 스타일로 코드를 짤 수 있도록 해준다.
  - 기존 코드와 동일하지만 내부적으로 비동기적으로 동작하도록
- thread-safe 보다 좀 더 코드를 짜는 것이 수월하다.
  - thread와는 다르게 greenlet은 선취적이지 않다. (greenlet이 자원적으로 yield하지 않으면, 다른 greenlet은 실행 될 수 없다.)
  - 중요한 section에서는 yield되지 않아야 하며, 그럴 경우 동기로 실행됩니다.
- 모든 라이브러리가 monkey-patched 될 수 있지는 않다. 
  - 만약 라이브러리가 blocking 동작을 하는 외부 C 라이브러리에 의존한다면 (MySQL-python, pylibmc, zc.zk 등) 그 라이브러리는 monkey-patched 될 수 없다.
  - 이러한 경우에는 순수 파이썬 구현 (pymysql, python-memcached, kazoo 등)으로 라이브러리를 교체해야한다. 


## synchronous & asynchronous execution

- 동시성 처리의 핵심은 큰 단위의 task를 한 번에 동기적으로 처리하는 것이 아닌
- 작은 단위의 subtask로 쪼개 동시에 비동기로 실행시키는 것이다.
- 두 subtask간 스위칭을 컨텍스트 스위칭이라고 부른다.
- 컨텍스트 스위칭은 yielding을 통해 이루어진다.
- 이 효과는 네트워크와 IO bound 함수를 이용할 때 극대화 된다.
- **gevent는 네트워크 라이브러리들이 암시적으로 Greenlet 컨텍스트들이 가능한 시점에 암시적으로 yield 하도록 보장한다.**
- sychronous 처리를 하는 경우에는 이전 작업이 끝날 때까지 blocking 되어 기다렸다가 다음 동작을 수행하지만, asychronouse 처리를 하는 경우는 이전 작업을 기다리지 않는다.
    - 동기작업은 작업 순서가 유지되고 오래 걸리지만
    - 비동기작업은 작업 순서가 유지되지 않으며 더 빠르게 끝난다.
    ```
    import gevent
    import random

    def task(pid):
        """
        Some non-deterministic task
        """
        gevent.sleep(random.randint(0,2)*0.001)
        print('Task %s done' % pid)

    def synchronous():
        for i in range(1,10):
            task(i)

    def asynchronous():
        threads = [gevent.spawn(task, i) for i in xrange(10)]
        gevent.joinall(threads)

    print('Synchronous:')
    synchronous()

    print('Asynchronous:')
    asynchronous()

    """
    Synchronous:  # 최대 0.02초 소요
    Task 1 done
    Task 2 done
    Task 3 done
    Task 4 done
    Task 5 done
    Task 6 done
    Task 7 done
    Task 8 done
    Task 9 done
    Asynchronous: # 최대 0.002초 소요
    Task 1 done
    Task 4 done
    Task 6 done
    Task 9 done
    Task 0 done
    Task 2 done
    Task 3 done
    Task 7 done
    Task 8 done
    Task 5 done
    """
    ```

**blocking I/O**
A
———————>
<——————-

B
———————>
<——————-

C
———————>
<——————-

---

**non-blocking I/O**
Input과 Output의 순서가 다를 수 있음
A 요청 중에 CPU를 사용하지 않는 작업 (네트워크 .. )을 할 때 자원을 다른 애가 쓸 수 있도록 넘겨줌.

A ———————>
B ———————>
C ———————>

A <——————-
B <——————-
C <——————-

대신에 CPU를 많이 사용하는 작업을 진행하게 되면, 오히려 좋지 않고 CPU를 많이 사용하는 작업은 task queue (ex. celery)에 던져주어야 함.

I/O가 긴 것과 CPU가 많이 드는 것이 섞여 있으면 안 됨
예전에 메렌서에서 섞여있었는데, 템플릿을 렌더링하면서 CPU를 많이 소모하게 되면서 메시지 전송을 못하게 되는 경우가 생겼음
그래서 지금은 웹인스턴스랑 워커가 나뉘어있음

## monkeypatching

- 런타임에 모듈, 클래스, 함수가 수정되는 것은 디버깅을 매우 어렵게 하기 때문에 "암시적인 부작용"을 만드는 안 좋은 생각이다.
- 그러나 파이썬의 기본 동작을 수정해야하는 경우에는 monkey patching 라이브러리를 이용하여 해결할 수 있다.
- monkey patching 을 통해 socket, ssl, threading, select와 같은 기본 라이브러리들의 blocking system call을 동시에 실행될 수 있도록 수정할 수 있따.
- 예로 Redis 파이썬 바인딩은 Redis 서버와 통신하기 위해 기본 tcp 소캣을 여는데, genvet.mock.patch_all()만으로도 Redis 바인딩이 요청들을 동시에 실행될 수 있도록 한다.
- monkey patch는 악이지만, 이런 경우 필요악이다.


[https://shifu-oh.tistory.com/10](https://shifu-oh.tistory.com/10)
[https://medium.com/@Pinterest_Engineering/how-we-use-gevent-to-go-fast-e30fa9f81334](https://medium.com/@Pinterest_Engineering/how-we-use-gevent-to-go-fast-e30fa9f81334)
