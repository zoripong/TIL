# mutable

- 주소에 할당 된 후 그 값이 변경 될 수 있는 것
- ex) 파이썬 기준 리스트, dict

```python
>>> x = [1, 2]
>>> y = x
>>> y.append(3)
>>> x
[1, 2, 3]
>>> y
[1, 2, 3]
```

# Immutable

- 주소에 할당 된 후 그 값이 변경 될 수 없는 것
- 값은 새로운 주소에 할당되고 변수는 새로운 주소를 가지고 있게 된다.
- ex) 파이썬 기준 숫자형, 문자열, 튜플

```python
>>> x = (1, 2)
>>> y = x
>>> y += (3, )
>>> x
(1, 2)
>>> y
(1, 2, 3)
```

# 번외

## call-by-value

함수 호출 시 매개변수로 변수의 값이 복사되어 넘어가는 방식

## call-by-reference

함수 호출 시 매개변수로 변수의 주소가 넘어가는 방식

## String vs StringBuffer vs StringBuilder

### String

- Immutable
- 문자열 변경시 Heap 메모리에 가비지가 많이 생성되기 때문에 성능에 안 좋은 영향을 끼친다.
- 멀티쓰레드 환경에서 안정성을 가지고 있음

### StringBuffer

- mutable
- 문자열 변경시 동일 객체내에서 수정하기 때문에 가비지가 생기지 않음
- 멀티쓰레드 환경에서 안정성을 가지고 있음

### StringBuilder

- mutable
- 문자열 변경시 동일 객체내에서 수정하기 때문에 가비지가 생기지 않음
- 멀티쓰레드에서 안전하지 않음
- 싱글쓰레드에서는 StringBuffer보다 성능이 뛰어남
