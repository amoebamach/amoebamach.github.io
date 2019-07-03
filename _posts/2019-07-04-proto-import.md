---
title: golang protobuf tip
key: 20190608
tags: golang go protobuf protoc  
mermaid: true
---

# Golang Protobuf import 팁
> 구글 protocol buffer를 활용하는 방법 중에서, 다른 *.proto 파일에 정의된 내용을 import하여 재사용하는 방법에 대하여 몇 가지 팁을 제시한다.

## 문제점
> Go 언어는 $GOPATH/src/ 이하 디렉터리에, 패키지 소스 코드를 넣어 관리한다.
> 사용자가 직접 작성한 코드도 $GOPATH/src/ 이하에 두어 관리한다,
>  
~~~bash
$GOPATH/ 
       └─/src
          ├─/example.com 
          │ └─/corp 
          │ └─/app 
          │ └─/rpc 
          │ ├─/user 
          │ ├─/blob 
          │ └─/log
          └─/example.org 
               └─/app 
                  └─/rpc
                     └─/main.go
~~~
> Golang 언어로 코드를 작성할 때, type이나 variable을 선언 및 사용하는 문법(syntax)은 다음과 같다.
~~~Go
// 패키지이름.타입이름
user.Message
~~~

>프로토콜 버퍼 '*.proto' 파일도 이를 따라야 한다.
 ---
## 해결책 
### Protocol Buffers 디렉터리 계층 정의
~~~bash
$GOPATH/src/example.com/
 └─/app
    └─/rpc
       ├─/user
       │ └─/user.proto
       ├─/blob
       │ └─/blob.proto
       ├─/log
       │ └─/log.proto 
       └─/main.proto
~~~
### 1.  proto file 작성
  >1-1) 참조 당하는 측(프로토 파일)
- user.proto
~~~Go
package user; 
option go_package = "example.com/app/rpc/user";
~~~
---
>1-2) 참조하는 측(proto file)
- main.proto
~~~Go
package rpc; 
option go_package = "example.com/app/rpc";
import "user/user.proto";
~~~
---
### 2. protoc-gen-go 옵션
- Go 코드를 생성하려면 다음 명령을 실행
```bash
$ protoc -Irpc --go_out=plugins=grpc:$GOPATH/src rpc/main.proto 
$ protoc -Irpc --go_out=plugins=grpc:$GOPATH/src rpc/user/user.proto
```
### 3. vscode-proto3 extension 사용시
> https://github.com/zxh0/_vscode_-_proto3_
> 에디터로 vscode를 사용하는 경우, 프로토콜버퍼관련 확장을 설치하는 경우가 있다.
> 이때, import를 사용하면, 신택스 하이라이트가 오동작하는 경우가 있다.
> - "import ...을 찾을 수 없다' 
> 
> 이런 종류의 에러가 발생하며 성가시게 할 수 있다.
> 해결책은 다음과 같다.
- 다음 settings.json 파일을 사용자의 홈디렉터리에 저장 
  - ~/.vscode/settings.json
- vscode 재시작 필요
  
~~~json
{
   "protoc" : {
     "path": "/usr/local/bin/protoc",
     "options": [
          "--proto_path=${workspaceRoot}/example.com/app/rpc",
     ]
   }
}
~~~
> 위에서 
> path : protoc 실행파일을 설치한 경로
> --proto_path={프로토파일을 저장할 디렉터리 루트}
>  * 혹자에 의하면, workspaceRoot를 생략해야 동작한다는 의견도 있음
>  * 필자는 자연스럽게 성공했음
---
## * 실패의 기록
> 다음은 위 답을 알기 전에 수행했던 시행착오 기록이다.
### 1) 실패:  option 을 생략한 경우
> 참조하는 측의 proto file
- main.proto
~~~Go
syntax="proto3"
package rpc; 

import "user/user.proto";
~~~
- 생성된 Go code
~~~Go
package rpc
import "user"   // 패키지를 레퍼런스(읽기)할 수 없음 
~~~

### 2) 실패 : go_package 옵션
> 참조되는 측
- user.proto

~~~Go
syntax = "proto3";
package user; 

option go_package = "example.com/app/rpc/user";
~~~

> 참조하는 측
- user.proto

~~~Go
syntax = "proto3";
package rpc; 

option go_package = "example.com/app/rpc";
import "user/user.proto"
~~~
---
- 생성명령
~~~bash
$ protoc -Irpc --go_out=plugins=grpc:. rpc/main.proto 
$ protoc -Irpc --go_out=plugins=grpc:. rpc/user/user.proto
~~~

- 결과
~~~bash
$GOPATH/
 └─/src
    └─/example.com
       └─/app
          ├─/example.com <- 불필요한 계층이 만들어진다
          │ └─/app
          │ └─/rpc
          │ ├─/user
          │ │ └─/user.pb.go
          │ └─/main.pb.go
          └─/rpc
             ├─/user
             │ └─/user.proto
             ├─/blob
             │ └─/blob.proto
             ├─/log
             │ └─/log.proto
             └─/main.proto
~~~
---
### 3) 실패 : protoc-gn-go 옵션 사용 시도
> protoc의 --go_out=XXXXX 
> 즉, 옵션을 통해 쉼표로 구분된 문자열로 다양한 옵션 구사 
#### (1) import_prefix
이 옵션은 지정된 문자열이 표준 패키지 이외 모든 부분에 붙는다. `protoc`에서 생성 된 패키지에만 붙는다면 문제가 있지만, `github.com/golang/protobuf/proto`머리에 붙어 버리기 때문에 좋지 않다.
#### (2) import_path
이것은 **.proto** 파일 `option go_package`이없는 경우에만 명령 줄에서 줄 수있는 옵션. 그래서  `option go_package`과 전혀  차이가 없다.

---
### 4) 실패 : vendor에 넣는 방법
`--go_out=`에서 대상 디렉토리가 결정되기 때문에, `option go_package`에서 vendor 다음에 넣어 버리자 방안.

> 기본적으로는 잘 동작했지만, `dep`에서 관리하지 않는 패키지가 vendor 다음에 들어가 버리기 때문에 `dep init`및 버튼 `dep ensure`류가된다. 현재로서는 무시할 수 없기 때문에 사용할 수 없다.
