# Generator

- iterator 를 생성해주는 function
- iterator 는 next() 함수를 이용해 데이터에 순차적으로 접근이 가능한 Object이다.
- yield 키워드를 통해 함수를 중단하고 호출한 쪽으로 값을 전달할 수 있다.
  - yield 되면 중단된 함수에서 사용한 데이터들은 모두 메모리에 남아있게 된다.
- 메인루틴으로 데이터를 전달하는 코루틴이 만들어진다.

## Generator 장점

1. 리스트 comprehension을 사용하는 것보다 메모리가 효율적이다.
item의 수와 상관없이 항상 동일한 메모리를 할당한다.
요소의 수가 많은 iterable을 관리해야 할 수록 더 효과가 차이난다.

```python 
>>> import sys
>>> sys.getsizeof( [i for i in range(100) if i % 2] )
520
>>> sys.getsizeof( [i for i in range(1000) if i % 2] )
4264
>>> sys.getsizeof( (i for i in range(1000) if i % 2) )
112
>>> sys.getsizeof( (i for i in range(100) if i % 2) )
112
```

2. Lazy evaluation이 가능하다.
수행시간이 긴 연산을 필요할 때까지 미룰 수 있다. 

```python
>>> import time
>>> def blocking(x):
...     print('sleep.... Zz...')
...     time.sleep(1)
...     return x
>>> for i in [blocking(x) for x in range(5)]:
...     print(i)
...
sleep.... Zz...
sleep.... Zz...
sleep.... Zz...
sleep.... Zz...
sleep.... Zz...
0
1
2
3
4
>>> for i in (blocking(x) for x in range(5)):
...     print(i)
...
sleep.... Zz...
0
sleep.... Zz...
1
sleep.... Zz...
2
sleep.... Zz...
3
sleep.... Zz...
4
```
