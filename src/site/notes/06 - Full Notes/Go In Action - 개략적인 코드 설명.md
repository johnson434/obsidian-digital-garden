---
{"dg-publish":true,"dg-permalink":"go-in-action-chapter-02","permalink":"/go-in-action-chapter-02/","noteIcon":""}
---

## Tags
- [[03 - Tags/Go in Action\|Go in Action]]
# 핵심 필기
**Go의 모든 파일은 패키지에 속한다.**
``` 
package main
```

**Go의 모든 init 함수는 main 함수보다 먼저 호출된다.**
``` go
func init() {
	// main 함수보다 먼저 실행된다.
}
```

**파일에 최상위 스코프에 변수가 소문자로 시작하면 다른 패키지에서 접근이 가능하고, 대문자로 시작하면 접근이 불가능하다.**
``` go
package search

# 소문자로 선언했으므로 타 패키지에서 접근이 불가능 
var matchers = make(map[string]Matcher)
# 대문자로 선언했으므로 타 패키지에서 접근이 가능
var AccessableMatchers = make(map[string]Matcher)
func init() {

}
```

**Map 선언 특이함**
``` go
# 이게 key가 string이고 value가 Matcher인 Map임;;;;;
make(map[string]Matcher)
```

**기본 타입은 보통 언어처럼 false, 0, "" 이런 값으로 초기화.** 
- 참조 타입은 해당 기반 타입의 초기화된 데이터 구조로 만들어진다.
- 참조 타입으로 선언된 변수 자체의 제로 값은 nil

**Goroutine 종료 대기 조건으로 세마포어 설정 가능**
``` go
var withGroup sync.waitGroup
# feeds의 길이만큼 세마포어의 값이 설정되어 
# 고루틴이 끝날 때마다 세마포어가 감소함
# 세마포어가 0이 되면 waitGroup이 모든 고루틴이 끝났다고 판단.
# main 함수가 더 실행한 코드가 없어도 
# waitGroup의 대기를 기다렸다가 프로그램이 종료됨.
waitGroup.Add(len(feeds))
```

**익명함수**
``` go
package main

import(
	"fmt"
	"sync"
)

func main() {
	var waitGroup sync.WaitGroup
	waitGroup.Add(1)

	// Goroutine으로 실행되는 익명함수다.
	go func(name string, number int32) {
		fmt.Printf("Name: %s, number: %d\n", name, number)
		waitGroup.Done()
	}("myname", 32)
	
	waitGroup.Wait()
	fmt.Println("Hello world")
}
```

함수가 끝날 때, close 해주는 `defer`
``` go
func RetrieveStudents() ([]*Student, error) {
	file, err := os.Open("data.json")
	if err != nil {
		return nil, err
	}

	defer file.Close() // 해당 함수가 끝나기 전에 file을 닫는다.

	var students []*Student
	err = json.NewDecoder(file).Decode(&students)

	return students, err
}
```

인터페이스의 접미사는 `er`
```
Decoder
Encoder
```

\_ 식별자
1. 값을 사용하지 않을 때
```
fmt main() {
	for i, _ := range slice {
		fmt.Println(i)
	}
}
```
2. package의 init 함수만 호출 후에 사용하지 않기 위해서
```
import "test" # 사용하지 않으면 컴파일러에서 경고를 보냄.
import _"test" # init 함수만 호출하고 사용하지 않아도 컴파일러에서 경고를 보내지 않음.
```

**구조체(struct) 설정**
1.  필드에 메타데이터를 설정 가능하다.
```
type SomeStruct struct {
	Name string `json:"site"`
}
```

**빈 인터페이스 활용**
1. interface{} 타입의 값을 전달 받을 수 있으며 reflect 패키지로 런타임 시에 타입을 결정할 수 있다.
``` go
func (dec *Decoder) Decode(v interface{}) error
```
- dec \*Decoder: 메서드 리시버로 Decoder 구조체의 메서드임을 나타낸다.

리시버
리시버의 포인터와 값 타입의 주요 차이는 "원본 데이터 수정 가능 여부"입니다.
- 포인터 리시버 (`*T`): 원본 데이터 수정 가능
- 값 리시버 (`T`): 데이터의 복사본으로 작업, 원본 데이터 수정 불가능
``` go
package main

import "fmt"

type People struct {
	name    string
	counter int
}

func (p *People) IncrementPtr() {
	p.counter++
}

func (p People) Increment() {
	p.counter++
}

func main() {
	people := new(People) # 포인터를 반환. Go에선 &People{} 형태가 더 많이 쓰임
	# 	people := &People{counter: 0}
	people.IncrementPtr()
	fmt.Println(people.counter)

	people2 := new(People)
	people2.Increment()
	fmt.Println(people2.counter)
}
```