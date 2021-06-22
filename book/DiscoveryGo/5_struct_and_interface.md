# 5. 구조체 및 인터페이스
---

# 구조체

- 필드들을 묶어 놓은 것
- 복잡한 자료형을 정의할 수 있음

## 구조체 사용하는 방법

```go
var task = struct {
	title string
	done bool
	due *time.Time
}{"laundry", false, nil}

type Task struct{
	title string
	done bool
	due *time.Time
}
myTask := Task{title: "laundry"} // 나머지 필드들을 기본값으로 채워짐
```

## const와 iota

```go
/*
enum 을 선언하는 방법:
const (
	UNKNOWN status = 0
	TODO    status = 1
  DONE    status = 2
)
아래 코드는 위와 동일하게 동작한다.
*/
type status int 

const (
	UNKNOWN status = iota
	TODO
	DONE
)

type Task struct {
    title string
		status status
		due *time.Time
}
```

- bool 타입 대신에 enum 을 사용하면 프로그램 확장에 용이하다.
- iota를 이용하면 KB, MB, GB 등의 동일한 간격으로 커지는 상수등을 정의할 때 용이하다.

## 테이블 기반 테스트

```go
func TestFib(t *testing.T) {
	cases := []struct {
		in, want int
	}{
		// 0. 0번째 피보나치 수는 0이어야 한다.
		{0, 0},
		// 1. 5번째 피보나치 수는 5이어야 한다.
		{5, 5},
	}
	for i, c := range cases {
		got := seq.Fib(c.in)
		if got != c.want {
			t.Errorf("[Case %d] Fib(%d) == %d, want %d", i, c.in, got, c.want)
		}
	}
}
```

- go 에서는 제네릭을 지원하지 않는다.
- 구조체와 배열을 이용하여 테이블 기반 테스트를 할 수 있다.
- 입력과 출력이 복잡해지는 경우 Index 도 함께 출력하면 어떤 테스트가 문제인지 파악하기 쉬워진다.

## 구조체 내장

- 구조체는 여러 자료형의 필드들을 가질 수 있다는 점이 가장 중요하다.
- 포함 관계 (Has A) 관계를 쉽게 표현할 수 있다.

```go
/*
마감 시간 필드 하나만 있는 구조체를 굳이 만들 필요없지만
직렬화 과정에서 의도대로 동작하지 않기 때문에 구조체로 정의한다.
*/
type Deadline struct {
	time.Time  // time.Time 구조체를 내장한 구조체
}

func (d *Deadline) OverDue() bool {
	// 메서드 리시버를 포인터로 바꾸어 deadline이 없는 경우에도 동작하도록 할 수 있다.
	return d != nil && time.Time(*d).Before(time.Now())
}

type Task struct {
	Title string
	Status status
	Deadline *Deadline  // *Deadline에 필드 이름을 생략하는 경우 
											// 아래 Task.OverDue는 없어져도 동작한다.
}

func (t Task) OverDue() bool {
	return t.Deadline.Overdue()
}
```

- 구조체를 내장하면 내장된 구조체에 들어 있는 필드들도 바로 접근이 가능하게 된다.
    - 따라서, 구조체 내장을 이용하면 여러 구조체에 있는 필드들이 모두 합쳐진 구조체를 만들 수 있다.
    - 실제로 합쳐진 것은 아니고 내부에 필드로 내장하고 있으면서 접근시 생략 가능하도록 편의를 제공하는 것 (상속의 개념이 아님)

# 직렬화와 역직렬화

- 직렬화: 객체의 상태를 보관이나 전송 가능한 상태로 변환하는 것
    - 구조체 뿐만 아니라 숫자, 문자열 등 모든 것을 직렬화 할 수 있다.
- 역직렬화: 직렬화와 반대로 보관되거나 전송받은 것을 다시 객체로 복원하는 것

## JSON

### JSON 직렬화 및 역직렬화

```go
func Example_marshalJSON() {
	t := Task {
		"Laundry",
		DONE,
		NewDeadline(time.Date(2015, time.August, 16, 15, 43, 0, 0, time.UTC)),
	}
	b, _ := json.Marshal(t)
	fmt.Println(string(b))
	// Output:
	// {"Title": "Laundry", "Status": 2, "Deadline": "2015-08-16T15:43:00Z"}
}

func Example_unmarshalJSON() {
	// `...` 사이에 있는 것들은 문자열이지만 그 사이에 있는 문자열들을 문자 그대로 사용하도록 함
	b := []byte(`{"Title": "Laundry", "Status": 2, "Deadline": "2015-08-16T15:43:00Z"}`)
	t := Task{}
	_ := json.Unmarshal(b, &t)
	fmt.Println(t.Title)
	fmt.Println(t.Status)
	fmt.Prinntln(t.Deadline.UTC())
	// Output:
	// Laundry
	// 2
	// 2015-08-16 15:43:00 +0000 UTC
}
```

- json 패키지는 대문자로 시작하는 필드들만 JSON으로 직렬화한다.

### JSON 태그

```go
type MyStruct struct {
	Title string `json:"title"`  // Title 대신 title을 json 필드로 이용
	Internal string `json:"-"`  // JSON에 나타나지 않고 무시됨
	Value int64 `json:",omitempty"`  // 0인 경우 JSON에 나타나지 않
	ID int64 `json:",string"`  // JSON으로 직렬화시 문자열로 직렬화
}
```

- 기본적으로 직렬화 되는 값이 변경되지 않을 때 구조체 필드에 json 태그를 붙이면 json 라이브러리가 읽고 처리해준다.
- 자바스크립트에서는 숫자형이 8바이트의 실수이기 때문에 정수값은 53비트를 넘어서면 정확도가 떨어지며, 낮은 자리 수들이 0으로 바뀌어버리는 문제가 생긴다.
    - 64비트 정수를 JSON으로 주고 받을 때에는 `json:,string` 을 해주는 것이 좋다.
    - 이렇게 하면 json 결과는 `"123"` 과 같은 10진수로 표현된 문자열이지만, 역직렬화해서 읽으면 정수형으로 변환된다.

### JSON 직렬화 사용자 정의

```go
// MarshalJSON을 통해 json.Marshaler 인터페이스를 구현함
// MarshalJSON을 구현하기만 해도 json.Marshaler가 될 수 있음
func (s status) MarshalJSON() ([]byte, error) {
	switch s {
	case UNKNONWN:
		return []byte(`"UNKNOWN"`), nil
	case TODO:
		return []byte(`"TODO"`), nil
	case DONE:
		return []byte(`"DONE"`), nil
	default:
		return nil, errors.New("status.MarshalJSON: unknown value")
	}
}

// UnmarshalJSON을 통해 json.Unmarshaler 인터페이스를 구현함
func (s *status) UnmarshalJSON(data []byte) error {
	switch string(data)
	case `"UNKNOWN"`: // json에서 문자열을 따옴표로 묶기 때문에 따옴표까지 함께 체크해야 함 
		*s = UNKNOWN
	case `"TODO"`:
		*s = TODO
	case `"DONE"`:
		*s = DONE
	default:
		return errors.New("status.UnmarshalJSON: unknown value")
	}
	return nil
}
```

- enum 타입의 경우 숫자로 직렬화/역직렬화가 이루어짐
- `MarshalJSON`, `UnmarshalJSON` 메서드를 구현하여 커스텀 코드를 작성할 수 있음
- enum 타입이 추가되면 고쳐주어야 하는 곳이 두군데나 있다.
    - 8장에서 go generate* 를 통해 코드를 자동으로 생성하는 법에 대해 배운다.

### 구조체가 아닌 자료형 처리

```go
// example of map
func Exmaple_mapMarshalJSON() {
	b, _ := json.Marshal(map[string]string{
		"Name": "Jhon",
		"Age": "14",
	})
	fmt.Println(string(b))
	// 맵은 순서가 없지만 직렬화 할 때 항상 키를 기준으로 정렬하여 출력
	// Output:
	// {"Age": "14", "Name": "Jhonn"}

	b, _ := json.Marshal(map[string]|interface{}{
		"Name": "Jhon",
		"Age": 16,
	})
	fmt.Println(string(b))
	// Output:
	// {"Age": 16, "Name": "Jhon"}
}
```

- map을 직렬화 할때는 반드시 Key는 문자열이어야 한다.
- interface는 map 도 담을 수 있기 때문에 json object 안에 json object가 있는 형태를 직렬화 할 수 있다.

### JSON 필드 조작하기

- json 라이브러리가 해주는 것은 많지만 잘 적용되지 않는 사례가 많다.
- 입출력하는 json의 구조에 따라 구조체의 구조가 제한되어 버린다.
- 특정 필드를 빼고 직렬화 하고 싶을 때
    1. 특정 필드가 제거된 구조체 자료형을 만든 다음에 그 자료형으로 모든 자료를 복사한 다음 직렬화 하는 방법
        - 번거롭고, 필드가 추가될 때마다 복사하는 코드도 변경해야 함
    2. 빼고자 하는 필드들의 값을 지운 다음에 직렬화 하는 방법
        - JSON 태그를 변경할 수 없기 때문에, 원래 구조에서 omitempty를 하고 싶지 않은 경우에는 필드 자체를 제외 시킬 방법이 없음
    3. 구조체 내장을 이용하기 (아래 예제)

```go
type Fields struct {
	VisibleField string `json:"visibleField"`
	InvisibleField string `json:"invisibleField"`
}

type AnotherFields struct {
	ExampleField string `json:"exmapleField"`
}

func ExampleOmitFields() {
	f := &Fields{"a", "b"}
	a := &AnotherFields{"d"}
	b, _ := json.Marshal(struct {
		*Fields
		*AnotherFields,
		InvisibleField string `json:"invisibleField,omitempty"`
		Additional String `json:"additional,omitempty"`
	}{Fields: f, AnotherFields: a, Additional: "c"}) // InvisibleField의 값이 초기화 되지 않았으므로 사라짐
	fmt.Println(string(b))
	// Output:
	// {"visibleField": "a", "exmapleField": "d", "additional": "c"}
}
```

## [Gob](https://golang.org/pkg/encoding/gob/)

- 언어에서 기본으로 제공하는 또 다른 직렬화 방식
- Gob은 Go 언어에서만 읽고 쓸 수 있는 형태
- 주고 받는 코드가 모두 Go 일 때 Gob 이용을 고려해볼 수 있음

# 인터페이스

- 메서드의 집합
- 의미상으로 인터페이스는 무언가를 할 수 있는 것을 의미
    - io.Reader는 Read 메소드들을 정의하는 자료형을 받을 수 있다.
    - 예를 들어 File은 Read 메소드가 있으므로 io.Reader가 될 수 있다.
    - 의미상으로는 읽을 수 있는 것을 의미
- 보통 인터페이스 이름은 끝에 -er을 붙인다.
- 인터페이스의 메서드를 모두 구현하기만 하면 해당 인터페이스를 구현한 것으로 취급 (Duck Typing)
- 빈 인터페이스는 어떤 자료형도 받을 수 있는 와일드카드
    - 어떤 자료형인지에 따라 다른 코드를 수행하는 형 스위치 등을 이용할 때 주로 사용

## 인터페이스 정의

```go
interface { // 인터페이스 이름 생략 가능
	Method()
}

type Loader interface {
	Load(filename string) error
}

type ReadWriter {  // 여러 인터페이스를 합칠 수 있음
	io.Reader
	io.Writer
}
```

## 커스텀 프린터

- String() 함수를 정의해주면 Print 계열 함수드이 해당 함수를 이용하여 출력함
    - fmt.Stringer라는 인터페이스는 func String() string 이라는 메서드를 가지고 있음

```go
func (t Task) String() string {
	check := "v"
	if t.Status != DONE {
		check = " "
	}
	return fmt.Sprintf("[%s] $s $s", check, t.Title, t.Deadline)
}

func main() {
	t := Task{}
	fmt.Print(t) // Print 함수가 내부적으로 인터페이스를 검사해서 Stringer인 경우 String 메서드를 호출하여 출력
}
```

## 정렬과 힙

- 정렬: 어떤 순서에 따라 늘어놓은 것
    - 두 자료를 비교했을 때 어떤 자료가 먼저오고, 어떤 자료가 나중에 오는지를 판단할 수 있어야 함
- 비교 정렬 (comparison sort): 두 자료를 비교하여 어느 것이 먼저 오는지 순서만 알아내서 정렬하는 방법
- 안정 정렬 (stable sort): 서로 같은 키에 대해 원래의 순서가 그대로 유지되는 정렬 방식
- Go의 sort.Sort는 비교 정렬이자 불안정 정렬을 이용하여 정렬함
    - 두 자료를 비교해 어느 자료가 더 먼저 와야하는지 결과를 돌려주는 부분을 작성하면 나머지 부분은 이미 만들어진 정렬 알고리즘을 이용하여 정렬됨

### 정렬 인터페이스 구현

```go
type CaseInsensitive []string  // 구조체가 아니어도 인터페이스를 구현할 수 있음

func (c CaseInsensitive) Len() int {
	return len(c)
}

func (c CaseSensitive) Less(i, j int) bool {
	return strings.ToLower(c[i]) < strings.ToLower(c[j]) || 
		(strings.ToLower(c[i]) == strings.ToLoswer(c[j]) && c[i] < c[j])
}

func (c CaseSensitive) Swap(i, j int) {
	c[i], c[j] = c[j], c[i]
}

func ExmapleCaseSensitive_sort() {
	apple := CaseSensitive([]string{
		"iphone", "iPad", "MacBook", "AppStore",
	})
	sort.Sort(apple)
	fmt.Println(apple)
	// Output:
	//[AppStore iPad iPhone MacBook]
}
```

- [sort.Interface](https://golang.org/pkg/sort/#Interface) 라는 인터페이스를 구현하면 정렬을 할 수 있음

### 정렬 알고리즘

- 일반적인 싱글 스레드에서는 QuickSort가 아주 훌륭하다.
    - 잘 구현된 QuickSort는 평균적으로 가장 효율적으로 정렬할 수 있다.
    - O(nlog n) 평균 복잡도를 가지지만, 최악의 경우 O(n^2)이 될 수 있다.
    - 이것을 극복하기 위해 랜덤 피벗을 뽑기도 하고, smaple pivot 방법을 사용하기도 한다.
- 7개 이하의 값들에 대해서는 삽입 정렬이 가장 효율적이다.
    - 삽입 정렬은 O(n^2)의 시간복잡도를 가지는 비효율적인 알고리즘이지만, 작은 크기에 자료에 대해서는 QuickSort보다 빠르다.
- sort.Sort에서는 기본적으로 빠른 정렬을 이용한다.
    - 빠른 정렬의 최악의 경우를 피하기 위해 피벗 3개를 골라서 가운데 값을 고르는 중위법을 이용
    - 너무 깊이 빠른 정렬에 빠지게 되면 힙 정렬을 이용

### 힙

- 자료 중에서 가장 작은 값을 O(log N)의 시간 복잡도로 꺼낼 수 있는 자료구조
- [heap.Interface](https://www.hanbit.co.kr/support/help_info.html)는 sort.Interface를 내장하고 있음

```go
/* 정렬 인터페이스 구현 예제와 이어지는 예제 */
func (c *CaseInsensitive) Push(x interface{}) {
	*c = append(*c, x.(string))
}

func (c *CaseInsensitive) Pop() Interface{} {
	len := c.Len()
	last := (*c)[len-1]
	*c = (*c)[:len-1]
	return last
}

func ExampleCaseInsensitive_heap() {
	apple := CaseInsensitive([]string{
		"iPhone", "iPad", "MacBook", "AppStore",
	})
	heap.Init(&apple)
	for apple.Len() > 0 {
		fmt.Println(heap.Pop(&apple))
	}
	// Outputh:
	// AppStore
	// iPad
	// iPhone
	// MacBook
}
```

- 단순히 정렬을 위해서는 힙 정렬을 사용할 필요가 없음
    - 시간 복잡도로 따지만 힙정렬은 O(n log n)이지만 일반적으로 빠른 정렬보다 느리고 메모리를 여기저기 임의로 엑세스 해야 하기 때문에 캐시를 효율적으로 사용할 수 없어서 더 느리다.
    - 그러나 빠른 정렬이나, 합병 정렬은 정렬이 모두 끝난 뒤에 정렬된 자료를 받아볼 수 있지만, 힙 정렬은 좀 더 일찍 첫 자료를 받아 볼 수 있다.
        - 정렬이 다  끝나지 않은 상황에서도 지금까지 정렬된 자료를 이용할 수 있는 것은 선택 정렬의 특징
        - 힙정렬도 선택 정렬의 일종으로 볼 수 있다.
- 힙 정렬은 효율적인 선택 정렬이기 때문에 정렬된 순서대로 받아보다가 어떤 조건을 만족하는 순가에 더 이상 자료가 필요없어지는 경우 유용하게 사용할 수 있음
- 생산자와 소비자가 따로 있고, 소비자는 현재 순서를 기달고 있는 값 중 가장 작은 값부터 소비해야 한다면 힙을 이용하는 것이 효율적
    - 이러한 방식을 우선순위 큐라고 부른다.
- 정렬된 자료를 합칠 때에도 힙을 이용하면 효율적이다.

## 외부 의존성 줄이기

```go
func Save(w io.Writer) {
	...
}
```

- 위와 같이 저장해야 할 파일 구조체가 아니라 쓰기 작업을 할 수 있는 io.Writer를 받으면 실제 구현에서는 파일을 넘기고, 테스트에서는 파일 대신 bytes.Buffer 같은 것을 넘겨 테스트할 수 있다.

```go
// 내가 사용할 파일 연산들을 인터페이스에 포함 시킴
type FileSystem interface {
	func Rename(oldpath, newpath string) error
	func Remove(name string) error
}

// 실제 구현
type OSFileSystem struct {}

func (fs OSFileSystem) Rename(oldpath, newpath string) error {
	return os.Rename(oldpath, newpath)
}

func (fs OSFileSystem) Remove(name string) error {
	return os.Remove(name)
}

func ManageFiles(fs FileSystem) {
	// 테스트에서는 가짜 파일시스템을 만들어 이용할 수 있음 
	...
}
```

## 빈 인터페이스와 형 단언

- 나열된 메서드들을 정의하고 있는 자료형은 인터페이스로 취급 될 수 있다는 원리에서 빈 인터페이스는 아무 자료형이나 취급할 수 있다는 뜻이 된다.
- 인터페이스는 실제로 자료형과 값을 갖고 있는 구조체로 표현이 됨
- 따라서 형변환을 할 때에는 자료형이 맞는지 실행 시간에 검사가 일어나야 함
    - 런타임에 단언한 형이 아닌 다른 타입이게 된다면 패닉을 발생시킴

```go
var r io.Reader = NewReader()
f := r.(os.File) // 인터페이스를 실제 자료형으로 변환하기 위한 형 단언, os.File이 아닌 경우 패닉 발생
f, ok := r.(os.File) // os.File이 아닌 경우 ok = false 
f, ok := r.(io.ReadCloser) // 다른 인터페이스로도 형단언이 가능
```

## 인터페이스 변환 스위치

- 포괄적으로 인터페이스를 받아서 특정 자료형일 때 구현을 달리하고 싶은 경우 자료형 스위치를 이용하면 쉽게 해결 할 수 있다.

```go
func Join(sep string, a ...interface{}) string {
	if len(a) == 0 {
		return ""
	}
	t := make([]string, len(a))
	for i := range a {
		switch x := a[i].(type) {
		case string:
			t[i] = x
		case int:
			t[i] = strconv.Itoa(x)
		case fmt.Stringer:
			t[i] = x.String()
		}
	}
	return strings.Join(t, sep)
}
```

- case에서 구체적인 자료형 (int, string)을 적어도 되고, 인터페이스 (fmt.Stringer)를 사용해도 되며 서로 섞어써도 된다.