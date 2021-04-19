## gevent

> Gevent is a library based on non-blocking IO (libevent/libev) and lightweight greenlets (essentially Python coroutines). Non-blocking IO means requests waiting for network IO won’t block other requests;

## greentlet

> greenlets mean we can continue to write code in synchronous style natural to Python.

> It’s easier to make code greenlet-safe than thread-safe because of the cooperative scheduling of greenlets. Unlike threads, greenlets are non-preemptive; unless the running greenlet voluntarily yields, no other greenlets can run. Keep in mind that the critical sections must not yield; if they do, they must be synchronized.

thread-safe 보다 좀 더 코드를 짜는 것이 수월하다.

thread와는 다르게 greenlet은 선취적이지 않다. (greenlet이 자원적으로 yield하지 않으면, 다른 greenlet은 실행 될 수 없다.)

중요한 section에서는 yield되지 않아야 하며, 그럴 경우 동기로 실행됩니다.

> But not all libraries can be monkey-patched. For example, if a library binds on external C library (e.g. MySQL-python, pylibmc, zc.zk) that does blocking operations, it can’t be monkey-patched. For these cases we needed to replace them with their pure-python implementation (e.g. pymysql, python-memcached, kazoo).

[https://medium.com/@Pinterest_Engineering/how-we-use-gevent-to-go-fast-e30fa9f81334](https://medium.com/@Pinterest_Engineering/how-we-use-gevent-to-go-fast-e30fa9f81334)

non-blocking I/O를 할 수 있도록 도와줌

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

---

I/O가 긴 것과 CPU가 많이 드는 것이 섞여 있으면 안 됨
예전에 메렌서에서 섞여있었는데, 템플릿을 렌더링하면서 CPU를 많이 소모하게 되면서 메시지 전송을 못하게 되는 경우가 생겼음
그래서 지금은 웹인스턴스랑 워커가 나뉘어있음

—

[https://shifu-oh.tistory.com/10](https://shifu-oh.tistory.com/10)