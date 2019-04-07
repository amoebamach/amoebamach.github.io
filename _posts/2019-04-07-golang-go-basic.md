---
title: TeXt - Mermaid
key: 20190406
tags: TeXt mermaid programming 
mermaid: true
---

# Golang에 대한 짧은 고찰

### 다시 Hello World
언제부터인가 프로그래밍 언어를 처음 접하게 되면 다음과 같이 "Hello World"라는 메시지를 화면/콘솔에 출력하는 프로그램을 작성하게 된다.
```Go
package main
import "fmt"
func main() {
   fmt.Println("Hello World")
}
```
go 언어에 처음이어도 그냥 이해가 될 것이다.
***
###  진보된 Hello World,... 응?
 ```Go
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
>  **golang.org**
### History 
> *어떤 사실에 대해 알아볼 때, 그의 역사/리뷰를 보는 것이 도움이 된다. 이는 다양한 분야에서 적용 가능한 방법론이라고 생각한다.

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
> [plan 9 ](https://en.wikipedia.org/wiki/List_of_Plan_9_applications)은 벨연구소에서 시도된 분산운영체제 프로젝트로 최근에는 좀 ... 하다. 분산운영체제는 참으로 어려운 영역이라고 생각한다.ㅎ~
  > [V8은 ](https://v8.dev/)구글이 만든 자바스크립트 엔진으로써, 크롬 브라우저에서 사용될 목적으로 제작되었고, 서버사이드에서 사용가능하게 확장되어 Node.js의 엔진으로 사용되고 있다. Mozilla의 [SpiderMonkey](https://developer.mozilla.org/ko/docs/SpiderMonkey)도 오픈소스 자바스크립트 엔진이다.
***
### 왜 Go 언어를 쓰는가?
 - 구글이 채택한  **확장(scale) 문제**에 대한 해결책이다.
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
### Java 를 계승한 것(Go언어 관점에서)
- Statically typed
- Garbage collected
- Memory safe(nil references, runtime bound checks)
- Methods
- Interfaces
- Type assertions(instanceof)
- Reflection
***
###  Java 기능에서 제외한 것(Go언어 관점에서)
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
- select 문장은 communication이 재개될 때 까지 블록(block)
```go
select {
	case n :=

Hi! I'm your **first Markdown** file in **StackEdit**. If you want to learn about StackEdit, you can read me. If you want to play with Markdown, you can edit me. Once you have finished with me, you can create new files by opening the **file explorer** on the left corner of the navigation bar.


# Files

StackEdit stores your files in your browser, which means all your files are automatically saved locally and are accessible **offline!**

## Create files and folders

The file explorer is accessible using the button in left corner of the navigation bar. You can create a new file by clicking the **New file** button in the file explorer. You can also create folders by clicking the **New folder** button.

## Switch to another file

All your files are listed in the file explorer. You can switch from one to another by clicking a file in the list.

## Rename a file

You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.

## Delete a file

You can delete the current file by clicking the **Remove** button in the file explorer. The file will be moved into the **Trash** folder and automatically deleted after 7 days of inactivity.

## Export a file

You can export the current file by clicking **Export to disk** in the menu. You can choose to export the file as plain Markdown, as HTML using a Handlebars template or as a PDF.


# Synchronization

Synchronization is one of the biggest features of StackEdit. It enables you to synchronize any file in your workspace with other files stored in your **Google Drive**, your **Dropbox** and your **GitHub** accounts. This allows you to keep writing on other devices, collaborate with people you share the file with, integrate easily into your workflow... The synchronization mechanism takes place every minute in the background, downloading, merging, and uploading file modifications.

There are two types of synchronization and they can complement each other:

- The workspace synchronization will sync all your files, folders and settings automatically. This will allow you to fetch your workspace on any other device.
	> To start syncing your workspace, just sign in with Google in the menu.

- The file synchronization will keep one file of the workspace synced with one or multiple files in **Google Drive**, **Dropbox** or **GitHub**.
	> Before starting to sync files, you must link an account in the **Synchronize** sub-menu.

## Open a file

You can open a file from **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Open from**. Once opened in the workspace, any modification in the file will be automatically synced.

## Save a file

You can save any file of the workspace to **Google Drive**, **Dropbox** or **GitHub** by opening the **Synchronize** sub-menu and clicking **Save on**. Even if a file in the workspace is already synced, you can save it to another location. StackEdit can sync one file with multiple locations and accounts.

## Synchronize a file

Once your file is linked to a synchronized location, StackEdit will periodically synchronize it by downloading/uploading any modification. A merge will be performed if necessary and conflicts will be resolved.

If you just have modified your file and you want to force syncing, click the **Synchronize now** button in the navigation bar.

> **Note:** The **Synchronize now** button is disabled if you have no file to synchronize.

## Manage file synchronization

Since one file can be synced with multiple locations, you can list and manage synchronized locations by clicking **File synchronization** in the **Synchronize** sub-menu. This allows you to list and remove synchronized locations that are linked to your file.


# Publication

Publishing in StackEdit makes it simple for you to publish online your files. Once you're happy with a file, you can publish it to different hosting platforms like **Blogger**, **Dropbox**, **Gist**, **GitHub**, **Google Drive**, **WordPress** and **Zendesk**. With [Handlebars templates](http://handlebarsjs.com/), you have full control over what you export.

> Before starting to publish, you must link an account in the **Publish** sub-menu.

## Publish a File

You can publish your file by opening the **Publish** sub-menu and by clicking **Publish to**. For some locations, you can choose between the following formats:

- Markdown: publish the Markdown text on a website that can interpret it (**GitHub** for instance),
- HTML: publish the file converted to HTML via a Handlebars template (on a blog for example).

## Update a publication

After publishing, StackEdit keeps your file linked to that publication which makes it easy for you to re-publish it. Once you have modified your file and you want to update your publication, click on the **Publish now** button in the navigation bar.

> **Note:** The **Publish now** button is disabled if your file has not been published yet.

## Manage file publication

Since one file can be published to multiple locations, you can list and manage publish locations by clicking **File publication** in the **Publish** sub-menu. This allows you to list and remove publication locations that are linked to your file.


# Markdown extensions

StackEdit extends the standard Markdown syntax by adding extra **Markdown extensions**, providing you with some nice features.

> **ProTip:** You can disable any **Markdown extension** in the **File properties** dialog.


## SmartyPants

SmartyPants converts ASCII punctuation characters into "smart" typographic punctuation HTML entities. For example:

|                |ASCII                          |HTML                         |
|----------------|-------------------------------|-----------------------------|
|Single backticks|`'Isn't this fun?'`            |'Isn't this fun?'            |
|Quotes          |`"Isn't this fun?"`            |"Isn't this fun?"            |
|Dashes          |`-- is en-dash, --- is em-dash`|-- is en-dash, --- is em-dash|


## KaTeX

You can render LaTeX mathematical expressions using [KaTeX](https://khan.github.io/KaTeX/):

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> You can find more information about **LaTeX** mathematical expressions [here](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference).


## UML diagrams

You can render UML diagrams using [Mermaid](https://mermaidjs.github.io/). For example, this will produce a sequence diagram:

```mermaid
sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```

And this will produce a flow chart:

```mermaid
graph LR
A[Square Rect] -- Link text --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
```

```go
func main() {
}
```