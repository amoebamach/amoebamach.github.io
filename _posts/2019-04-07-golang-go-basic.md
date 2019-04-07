---
title: TeXt - Mermaid
key: 20190406
tags: TeXt mermaid programming 
mermaid: true
---

# Golang에 대한 짧은 고찰

### 다시 Hello World
언제부터인가 프로그래밍 언어를 처음 접하게 되면 다음과 같이 "Hello World"라는 메시지를 화면/콘솔에 출력하는 프로그램을 작성하게 된다.

```go
package main
import "fmt"
func main() {
   fmt.Println("Hello World")
}
```
go 언어에 처음이어도 그냥 이해가 될 것이다.

***
###  진보된 Hello World,... 응?

 ```go
package main
import "fmt"
func main() {
	ch := sayHello("World")
	for str := range ch {
			fmt.Println(str)
	}
}

func sayHello(name string) <- chan string {
	ch := make(chan string)
	go func() {
			defer close(ch)
		
			ch <- "hello"
			ch <- name
	}()
	return ch
}
```

### Go 에 대한 재미 없는 정의

> GO 는 오픈 소스 프로그래밍 언어인데, **간단**하고, **신뢰성** 있으며, **효율**적인 소프트웨어를 빌드하는 것을 쉽게 만들어 주는 도구이다.
> 
>  **golang.org**

### History 

> *어떤 사실에 대해 알아볼 때, 그의 역사/리뷰를 보는 것이 도움이 된다. 이는 **다양한 분야에서 적용 가능한 방법론**이라고 생각한다.

 - 2009, 오픈 소스 Go 언어 시작 
 - 2012, Go 버전 1, 안정버전 릴리즈 
 - 2019.4, 최신버전  go1.12.2[---golang.org참조](golang.org)
 - 2019, [Tiobe 언어 순위 보기 ](https://www.tiobe.com/tiobe-index/)
 
 ***
 
### 설계자
     
      
|  Designer | profile |
|--|--|
| Robert Griesemer | V8 JavaScript engine, Java HotSpot VM |
| Rob Pike | Unix, Plan 9, UTF-8  |
| Ken Thompson | Unix Plan 9, 8 language, UTF-8 |

> - [plan 9 ](https://en.wikipedia.org/wiki/List_of_Plan_9_applications)은 벨연구소에서 시도된 분산운영체제 프로젝트로 최근에는 좀 ... 하다. 분산운영체제는 참으로 어려운 영역이라고 생각한다.ㅎ~
>
> - [V8은 ](https://v8.dev/)구글이 만든 자바스크립트 엔진으로써, 크롬 브라우저에서 사용될 목적으로 제작되었고, 서버사이드에서 사용가능하게 확장되어 Node.js의 엔진으로 사용되고 있다. Mozilla의 [SpiderMonkey](https://developer.mozilla.org/ko/docs/SpiderMonkey)도 오픈소스 자바스크립트 엔진이다.

***

### 왜 Go 언어를 쓰는가?
 - Go는 구글이 채택한  **확장(scale) 문제**에 대한 해결책이다.

***

### 시스템 스케일(규모)
- 1,000,000 대 규모로 확장되는 시스템
- 각 작업(job)은 1,000 대의 장비에서 실행
- 작업이 지정/할당되고, 시스템 내의 다른 구성요소와 상호 작용
- 한번에 다수 개의 작업이 실행

> 해결책 -->  **동시성(concurrency)에 대한 강력한 지원**이 필요

***

### 엔지니어링 스케일

- 5,000 명 이상의 개발자가 40개 이상의 사무실에서 작업
- 분당 20개 패키지가 변경됨
- 매달 50% 코드 베이스가 변경됨
- 매일 5천만 건의 테스트 케이스가 실행됨
- 단일 코드 트리
- 해결책 : **거대한 코드 베이스**들을 **지원하는 언어** 설계

***

### Go 언어를 사용하는 프로젝트/제품/제품
- 구글
- Docker
- Qiniu
- TiDB(Distributed SQL Database)

***

###  Java 와의 비교(Go언어 관점에서)
|기능|Go언어 지원 여부  |비고|
|--|--|--|
| static type | o ||
| garbage collection | o |
| memory safe | o |nil references, runtime bound checks|
| method | o |
| interface | o |
| type assertion | o | instanceof |
| reflection | o |
| class | x |
| constructor | x |
| inheritance | x |
| final | x |
| exception | x |
| annotation | x |
| 사용자 정의 generic| x |

- 기타
	- 프로그램은 머신코드로 컴파일/빌드됨(가상머신 없음)
	- 정적 링크 바이너리(단일 실행파일)
	- control over memory layout
	- function values and lexical closures
	- Built-in strings(UTF-8)
	- Built-in generic maps and arrays/slices
	- Built-in concurrency

***

### Built-in Concurrency
- CSP - Communication sequential processes(Hoare, 1978)
> - sequenctial execution is easy to understand. Async callbacks are not.
> "Don't communicate by sharing memory, share memory by communicating."
- CSP in Golang : go routines, channels, select

***

### Goroutines
- Goroutines 은 thread와 유사
- 아주 작은 stack을 가지고 시작되며, 필요시 크기 변경
- Go 프로그래은 수백~수천개의 goroutine을 가질 수 있음
- go funcion(args) 형식으로 고루틴(goroutine) 실행
- Go runtime은 goroutine을 OS threads로 스케쥴

***

### Channels
- go-routine들 간에 통신(communication) 제공

```go
c := make(chan string)

// go-routine 1
c <- "hello!"

// go-routine 2
s := <- c
fmt.Println(s)   // "hello!"

```

### Buffered vs. Un-buffered Channels
- un-buffered : co-routine ?
- buffered : producer/consumer 

```go
package main
import "fmt"
func main() {
	ch := sayHello("World")
	for str := range ch {
			fmt.Println(str)
	}
}

func sayHello(name string) <- chan string {
	ch := make(chan string)
	go func() {
			defer close(ch)
		
			ch <- "hello"
			ch <- name
	}()
	return ch
}
```

### select
- 아래 select 문장은 '채널에 입력'/'communication이 재개'될 때 까지 블록(block)
```go
select {
	case n := <- foo:
		fmt.Println("received from foo", n)
	case m := <- bar:
		fmt.Println("received from bar", n)
	case <- time.After(10*time.Second)
		fmt.Println("time out")
}
```
***
## Function & Closure
### local variable escape

```c++
#include <string>
#include <iostream>
#include <functional>

using namespace std;

function<void()> numberPrinter(int x) {
	return [&]() {
		cout << "Printer: " << x << endl;
	};
}

int main() {
	numberPrinter(123)();
}
```

### Hello World again
go func()을 주의 깊게 보기 
 ```go
package main
import "fmt"
func main() {
	ch := sayHello("World")
	for str := range ch {
			fmt.Println(str)
	}
}

func sayHello(name string) <- chan string {
	ch := make(chan string)
	go func() {
			defer close(ch)
		
			ch <- "hello"
			ch <- name
	}()
	return ch
}
```

***
### struct
- control over memory layout
- value vs. pointer(reference)
```go
type Address struct {
	Id int
	Name string
	Address struct {
		City string
		Post string
	}
}
// 모든 모델에서 이러한 루틴을 구현
func (s *Address) String string {
	if s == nil {
		return "N/A"
	}
	return fmt.Sprintf("Address Id = %d", s.Id)
}
```
***
### Method & Interface
- Simple interfaces
- Duck type

```go
// in package "fmt"
type Stringer interface {
	String() string
}

```

### Interface and Type assertion

- empty interface{} means any type

```go
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}
```

***

## Compiled to binary
- Run fast
- Compile fast
- Deploy fase

> - 참고 : [go 언어 벤치마크](https://benchmarksgame-team.pages.debian.net/benchmarksgame/faster/go.html) 
> - c/gcc < Go < C# .Net <= Java << Python 3

***
