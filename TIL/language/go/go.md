# GO
--- 

> Go code is grouped into packages, and packages are grouped into modules.

> If you publish a module, this must be a path from which your module can be downloaded by Go tools. That would be your code's repository.

> In Go, a function whose name starts with a capital letter can be called by a function not in the same package.

Note that randomFormat starts with a lowercase letter, making it accessible only to code in its own package (in other words, it's not exported).

> In Go, code executed as an application must be in a main package.

> run the go mod tidy command to synchronize the [example.com/hello](http://example.com/hello) module's dependencies, adding those required by the code, but not yet tracked in the module.

> A slice is like an array, except that its size changes dynamically as you add and remove items.

> Go executes init functions automatically at program startup, after global variables have been initialized.

> In Go, you initialize a map with the following syntax: make(map[key-type]value-type).

> While the go run command is a useful shortcut for compiling and running a program when you're making frequent changes, it doesn't generate a binary executable.

> A field declared with a type but no explicit field name is called an embedded field.

- go 코드를 작성할 때에는 세미콜론을 붙이지않는다. go 컴파일러는 컴파일 시점에 줄이 끝난 것으로 추측되는 경우 세미콜론을 자동으로 붙여준다.
    - 쉼표, 연산자, 여는 괄호 등으로 끝나면 줄이 끝나지 않은 것으로 판단한다.
- go는 정적 타입의 언어, 실행시점에 변수의 자료형이 변경될 수 없음
- int, unit 타입은 실행 환경에 따라 32비트, 64비트가 될 수 있다.
    - 특정 비트로 고정하고 싶다면 int64 등과 같이 명시해주어야한다.
    - 

```bash
$ gofmt -w .  // 전체 자동 포매팅 적용
```

[https://golang.org/doc/effective_go#embedding](https://golang.org/doc/effective_go#embedding)

- 리턴 값에 이름을 붙여줄 수 있다.

```go
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return  // 인자가 없는 경우 리턴값에 붙인 이름의 변수가 리턴된다. (naked return)
}
```

- `:=` 는 var 를 줄인 짧은 변수 선언이다. 함수 밖에서는 사용할 수 없다.
- Go 의 기본 자료형

    ```
    bool

    string

    int  int8  int16  int32  int64  // int는 시스템의 bit를 따라감
    uint uint8 uint16 uint32 uint64 uintptr

    byte // uint8의 별칭

    rune // int32의 별칭
         // 유니코드에서 code point를 의미합니다.

    float32 float64

    complex64 complex128 // 복소수를 나타냄 (실수와 허수의 합으로 표현한 수)
    										 // 허수는 제곱해서 음수가 나오는 수를 의미 (존재하지 않는 수)
    ```

- 정수 값이 필요할 때에는 특정한 이유로 크기를 정해야하거나 unsigned 정수 type을 사용해야하는 게 아니라면 int 를 사용해야합니다.
- defer는 stack 구조로 쌓인다.
- 구조체 v에 대한 포인터 p를 이용하여 v의 값을 가져오고 싶을 때에는 (*p).X를 줄어 p.X로 표현할 수 있다.
    - 함수도 마찬가지로 사용할 수 있다. 리시버가 포인터형인 경우 값을 포인터형으로 변환해주고, 값인 경우 포인터형을 값으로 변환해준다.
- Go 는 클래스를 가지고 있지 않다. 하지만 receiver 인자가 있는 함수를 구현할 수 있다.
    - 리시버는 구조체뿐만 아니라 Go 기본 자료형도 될 수 있다. (다른 패키지에 정의된 리시버로는 정의 불가능)

    ```go
    type MyFloat float64

    func (f MyFloat) Abs() float64 {
    	if f < 0 {
    		return float64(-f)
    	}
    	return float64(f)
    }
    ```

- 포인터 리시버의 장점
    - 값을 수정할 수 있게 된다.
    - 구조체가 큰 경우 효율적으로 호출할 수 있다.
    - 값에 의한 리시버 경우는 값을 복제하기 때문에 의도대로 동작하지 않을 뿐더러, 자원이 낭비된다.
        - 따라서 값이 수정할 필요가 없는 경우에도 포인터 리시버로 정의하는 것이 좋다.
- 구조체가 포인터 리시버를 이용하여 인터페이스를 구현했더라도, 값에 의한 인터페이스 함수 실행은 불가능하다.

    ```go
    package main

    import (
    	"fmt"
    	"math"
    )

    type Abser interface {
    	Abs() float64
    }

    func main() {
    	var a Abser
    	v := Vertex{3, 4}

    	a = &v // a *Vertex implements Abser

    	// In the following line, v is a Vertex (not *Vertex)
    	// and does NOT implement Abser.
    	a = v

    	fmt.Println(a.Abs())
    }

    type Vertex struct {
    	X, Y float64
    }

    func (v *Vertex) Abs() float64 {
    	return math.Sqrt(v.X*v.X + v.Y*v.Y)
    }
    ```

    - 인터페이스의 구현체가 nil인 경우 구현된 함수를 호출하는 것은 괜찮지만, 인터페이스의 구현체가 없는체로 무엇도 할당하지 않고 구현된 함수를 호출하면 에러가 발생한다.
    - 비어있는 인터페이스는 모든 유형을 가질 수 있으며, 알 수 없는 값을 처리할 때 유용
    - type을 가지고 switch 분기를 사용할 수 있음

    ```go
    switch v := i.(type) {
    	case int:
    		fmt.Printf("Twice %v is %v\n", v, v*2)
    	case string:
    		fmt.Printf("%q is %v bytes long\n", v, len(v))
    	default:
    		fmt.Printf("I don't know about type %T!\n", v)
    	}
    ```

    - 파이썬에서 `__str__` 함수를 오버라이딩하는 것처럼 fmt 패키지의 String() string 내장 인터페이스를 구현하여 사용할 수 있음
        - `Error() string` 내장 인터페이스를 이용하여 에러를 커스텀할 수 있음
    - 고루틴은 고 런타임에 의해 관리되는 경량 스레드이다.
        - 평가(=계산?, 정의?, 할당?)는 현재 고루틴에서 이루어지고 실행은 새로운 고루틴에서 일어난다.
        - 고루틴은 동일한 주소 공간에서 동작하기 때문에 공유 자원에 접근하는것은 반드시 동기적으로 작동되어야 한다.
        - Go에 다른 기본형들이 존재하는 것처 당신이 Go에서 sync와 관련된 기본 기능이 필요없다하더라도 sync 패키지는 유용한 기본형을 제공합니다.
    - 채널은 고루틴에서 값을 주고 받을 수 있는 하나의 분리된 통로
        - 채널은 미리 정의가 되어야만 하며
        - 전송과 수신은 다른 한쪽이 준비 될 때까지 block된다.
        - 명시적인 lock이나 조건 변수 없이 고루틴이 동기적으로 동작할 수 있도록한다.
        - 채널은 버퍼를 가질 수 있으며, 두번째 인자로 버퍼 길이를 지정할 수 있다.

            ```python
            ch := make(chan int, 2)
            ```

    - 전송자는 더이상 보낼 데이터가 없다는 것을 암시하기 위해 channel을 close 할 수 있다.
        - 수신자는 채널의 두번째 매개변수를 통해 채널이 닫혔는지 확인할 수 있다.

        ```python
        v, ok := <- ch // 더 수신할 값이 없으며 채널이 닫혀있는 경우 ok는 false
        ```

        - * 수신자가 아닌 전송자만이 채널을 닫아야 한다. 닫힌 채털에 데이터를 전송하는 것은 panic을 야기한다.
        - * 채널은 반드시 닫지 않아도 된다. 채널을 닫는 것은 수신자가 더 이상 들어올 값이 없다는 것을 알아야 하는 경우에만 필요하다.
        - range를 통해 채널이 열려있는 동안 데이터를 읽어오는 로직을 만들 수 있다.

        ```go
        package main

        import (
        	"fmt"
        )

        func fibonacci(n int, c chan int) {
        	x, y := 0, 1
        	for i := 0; i < n; i++ {
        		c <- x
        		x, y = y, x+y
        	}
        	close(c)
        }

        func main() {
        	c := make(chan int, 10)
        	go fibonacci(cap(c), c)
        	for i := range c {
        		fmt.Println(i)
        	}
        }
        ```

    - `select` 문을 이용하여 채널이 여러개인경우 데이터가 들어온 채널을 골라 동작할 수 있도록 한다.

        ```go
        package main

        import "fmt"

        func fibonacci(c, quit chan int) {
        	x, y := 0, 1
        	for {                 // 2) fibonacci 함수가 호출되며 무한루프 시작
        		select {
        		case c <- x:        //  3) c 채널에 값 전달
        			x, y = y, x+y
        		case <-quit:        // 6) quit 채널에 값 전달 받아서 함수 종료
        			fmt.Println("quit")
        			return
        		}
        	}
        }

        func main() {
        	c := make(chan int)
        	quit := make(chan int)
        	go func() {
        		for i := 0; i < 10; i++ {
        			fmt.Println(<-c)  // 1) fibonacci 안에서 c 채널에 값을 넣어줄 때까지 대기
        							// 4) fibonnaci 안에서 c 채널에 10번 넣어주는 경우 해당 for문을 벗어남
        		}
        		quit <- 0           // 5) for문 10번 루프 후 quit 채널에 신호 전달
        	}()
        	fibonacci(c, quit)
        }
        ```

        - 파이썬 제너레이터와는 다르게 양방향 통신이 가능함
        - 고루틴에서 메인루틴으로, 메인루틴에서 고루틴으로 데이터 전달이 자유로움
- 다른 case들이 준비 되지 않은 경우 default case를 이용하여 액션을 정의 할 수 있음

    ```go
    package main

    import (
    	"fmt"
    	"time"
    )

    func main() {
    	tick := time.Tick(100 * time.Millisecond)  // 0.1초마다 트리거
    	boom := time.After(500 * time.Millisecond)  // 0.5초후에 트리거
    	for {
    		select {
    		case <-tick:
    			fmt.Println("tick.")
    		case <-boom:
    			fmt.Println("BOOM!")
    			return
    		default:
    			fmt.Println("    .")
    			time.Sleep(50 * time.Millisecond)  // 0.05초간 sleep 
    			// (잠시라도 sleep 하지 않으면 timeout이 발생하는데 왜일까? :thinking:
    			// Print하느라 다른 채널에 값이 들어온 것을 처리하지 못하는 것일까?
    		}
    	}
    }

    /*
        .
        .
    tick.
        .
        .
    tick.
        .
        .
    tick.
        .
        .
    tick.
        .
        .
    tick.
    BOOM!
    */
    ```

- goroutine을 활용하여 이진 트리 탐색하기

    ```go
    package main

    import (
    	"fmt"
    	"golang.org/x/tour/tree"
    )

    // Walk walks the tree t sending all values
    // from the tree to the channel ch.
    func Walk(t *tree.Tree, ch chan int) {
    	defer close(ch)
    	
    	// closure 활용하여 함수 종료시 channel 닫을 수 있게함
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

    // Same determines whether the trees
    // t1 and t2 contain the same values.
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

- 공유 자원에 대해 여러 고루틴이 참여하며 생기는 race condition 문제를 방지하기 위해 Mutex를 제공함
    - Lock과 Unlock 함수를 제공하며, Lock과 Unlock 사이에 있는 코드는 보호된다.
    - 공유 자원에 map에 대해 lock을 하지 않고 실행하면, `concurrent map writes` 에러가 발생한다.
    - lock 이후 unlock을 하지 않으면 다른 고루틴들이 접근 할 수 없기 때문에 데드락이 발생하게 된다.
        - defer를 이용하여 함수가 끝날 때 반드시 unlocked될 것을 명시할 수 있다.

- [고루틴의 활용 예](https://stackoverflow.com/questions/19747950/when-should-i-use-concurrency-in-golang)
    - 어떠한 producer-consumer 구조에서나 적절히 사용할 수 있다. 2개의 고루틴을 이용하여 채널로 결과물을 producer가 consumer에게 전달하는 식으로 활용할 수 있다.
    - 다른 좋은 동시성 예는 여러 I/O 작업 (디스크, 네트워크, 터미널 등)을 할 때 좋다.