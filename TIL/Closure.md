# Closure?

- 외부함수가 소멸된 이후에도 내부함수가 외부함수 변수에 접근 가능한 것
- 내부함수가 소멸될 때까지 외부함수의 지역변수가 소멸되지 않는 특성

```python
function outter(){
    var title = 'coding everybody';  
    return function(){        
        alert(title);
    }
}
inner = outter();
inner();
```
