# Closure?

- 외부함수가 소멸된 이후에도 내부함수가 외부함수 변수에 접근 가능한 것
- 내부함수가 소멸될 때까지 외부함수의 지역변수가 소멸되지 않는 특성

> example 1

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

> example 2
*go-tour 의 goroutine 문제*

```go
package main

import (
	"fmt"
	"golang.org/x/tour/tree"
)

func Walk(t *tree.Tree, ch chan int) {
	defer close(ch)
	
	// closure 활용하여 재귀 함수 종료시 channel 닫을 수 있게함
	var walk func(t *tree.Tree)
	walk = func(t *tree.Tree) {
		if t.Left != nil {
			walk(t.Left)
		}
		ch <- t.Value
		if t.Right != nil {
			walk(t.Right)
		}
	}
	
	walk(t)
}

func Same(t1, t2 *tree.Tree) bool {
	c1 := make(chan int)
	c2 := make(chan int)
	go Walk(t1, c1)
	go Walk(t2, c2)
	
	for {
		v1, ok1 := <- c1
		v2, ok2 := <- c2
		
		// 항상 정렬 되어있으므로 순서대로 나온 값이 다르거나,
		// 길이가 달라 둘 중 하나라도 먼저 끝나게 된다면
		// 동일한 이진트리라고 볼 수 없음
		if v1 != v2 || ok1 != ok2 {
			return false
		}
		
		// 더이상 가져올 값이 없다면,
		// 모든 트리를 탐색하는 동안 다른 점이 없었던 것이므로
		// 동일한 트리라고 볼 수 있음
		if !ok1 {
			break
		}
	}
	return true

}


func main() {
	ch := make(chan int)
	
	go Walk(tree.New(1), ch)
	
	for i := 0; i < 10; i++ {
		fmt.Println(<-ch) 
	}
	
	result1 := Same(tree.New(1), tree.New(1))
	result2 := Same(tree.New(1), tree.New(2))
	fmt.Println(result1)
	fmt.Println(result2) 
}
```