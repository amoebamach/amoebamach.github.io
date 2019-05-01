---
title: gorm
key: 20190501
tags: gorm go golang mariadb mysql orm 
mermaid: true
---

# gORM - 설치 및 기본 사용
> gORM은 go언어에서 사용 가능한 ORM(Object Relation Mapping) 라이브러리이다.
> 홈페이지 :  http://gorm.io
## 전제(선행) 조건
- mariadb  설치
	- 또는, 호환 데이터베이스(mysql, percona 등)
- golang(go) 개발 환경 설치
- (약간) SQL에 대한 지식
---
## mariadb에 테스트 사용자 생성 및 권한부여

#### 1) bash에서 root 사용자로 mysql root 사용자 접속
```bash
sudo su -          # 리눅스 root 사용자 전환
mysql -u root      # mysql/mariadb의 root 사용자로 접속
```
### 2) mysql 에서 사용자 생성 및 권한 부여 

아래 일련의 mysql/mariadb 명령은 다음과 같은 일을 수행한다.

-  'testuser'라는 mysql사용자 생성
	- localhost 접속 및 원격 IP 접속한 경우 포함
- 'testuser'에게 모든 권한 부여
	- localhost 접속  및 원격 IP접속한 경우 포함

테스트를 위해 아래와 같이 mariadb에 접속하여 testuser 라는 사용자를 생성하고, 데이터베이스 생성 권한을 포함한 모든 권한을 부여한다.
 
```sql
create user 'testuser'@'%' identified by 'testpass';
create user 'testuser'@'localhost' identified by 'testpass';
GRANT ALL PRIVILEGES ON *.* TO 'testuser'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'testuser'@'localhost';
FLUSH PRIVILEGES;
```
> 확인 방법
>  select * from mysql.user where user = 'testuser' \G
>  - testuser의 세팅정보를 조회
>  - \G 를 세미콜른 ( ; ) 대신, 입력하고, 엔터입력하면 레코드를 세로로 출력
>  - 주요 필드 값
>  Create_priv: Y
             Drop_priv: Y
           등 모든 프리빌리지 값이 Y로 되어 있음을 확인하도록 한다.
---
> - 주의
>  실제 데이터베이스 운영시에는 데이터베이스 보안을 위해 사용자에게 제한된 프리빌리지를 부여 하도록 해야 한다.  
---

## gORM 설치
go언어의 다양한 라이브러리/도구 설치하듯이 다음 명령을 터미널(bash, cmd창 등)에서 실행하여 설치한다.
```bash
go get -u github.com/jinzhu/gorm
```
> 위 명령에 대해 간단히 설명하면 다음과 같다.
> https://github.com/jinzhu/gorm 사이트에 접속하여, 소스를 다운로드하여
> 이 소스를 로컬컴퓨터의 $GOPATH/src/ 디렉터리 이하에, 동일한 디렉터리 구조로 저장한다. 즉,  $GOPATH/src/github.com/jinzhu/gorm 디렉터리 이하에 저장한다(clone).
> -u 옵션에 의해, gorm이 내부적으로 사용하는 패키지 (의존성)는 자동으로 다운로드하여  $GOPATH/src/ 디렉터리 이하에 인터넷 URL을 경로로 소스를 다운로드 하여 저장한다.
> 저장이 완료되면, 각 소스를 빌드하여 라이브러리(패키지 ; go언어는 패키지라는 용어를 선호)를 생성한다. 라이브러리는 정적(static) 라이브러리로 만들어지고, 이 파일명은 유닉스/리눅스와 유사한 이름 규칙으로 만들어지고, $GOPATH/pkg/ 디렉터리 이하에 라이브러리 확장자인 *.a 형태로 저장된다.
>  참고로, 다운로드한 패키지가 실행파일(유틸리티,도구)인 경우에는 실행파일을 만들어, 이를 $GOPATH/bin 이하에 생성 및 저장한다.
>```bash
>mach@mach-W650EH:~$ ls /mach/go/pkg/linux_amd64/github.com/jinzhu/
>gorm.a
>mach@mach-W650EH:~$ file /mach/go/pkg/linux_amd64/github.com/jinzhu/gorm.a 
>/mach/go/pkg/linux_amd64/github.com/jinzhu/gorm.a: current ar archive
>mach@mach-W650EH:~$
>```
---
## gORM 용 MySQL 드라이버 설치
```bash
go get github.com/go-sql-driver/mysql
```
---
## MySQL 쿼리 로그 설정
- mysql 쿼리를 추적하기 위하여 로그를 설정
```mysql
MariaDB [(none)]> SET GLOBAL general_log = 1;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> SELECT @@log_output, @@general_log, @@general_log_file;
+--------------+---------------+--------------------+
| @@log_output | @@general_log | @@general_log_file |
+--------------+---------------+--------------------+
| FILE         |             1 | mach-W650EH.log    |
+--------------+---------------+--------------------+
1 row in set (0.00 sec)

MariaDB [(none)]>
```
```bash
tail -f /var/log/mysql/mysql.log
```

---
### 1.  MySQL 간단 연결
```go
package main

import (
	"fmt"
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
)

func  main() {
	// Create the database handle, confirm driver is present
	db, _  := sql.Open("mysql", "testuser:testpass@tcp(127.0.0.1:3306)/")
	defer db.Close()

	// Connect and check the server version
	var  version  string
	db.QueryRow("SELECT VERSION()").Scan(&version)
	fmt.Println("Connected to:", version)
}
```
실행 및 결과
```bash
$ go build
$ ls
main.go  tutorial
$ ./tutorial 
Connected to: 10.1.38-MariaDB-0ubuntu0.18.04.1
$
```
---
## 2. 데이터베이스 생성 예제
```go
// 데이터베이스 생성 예제
package main

import (
	"github.com/jinzhu/gorm"

	_ "github.com/go-sql-driver/mysql"
)

func  main() {
	// mysql 드라이버를 사용하여, 데이터베이스에 접속
	// 사용자 : testuser, 비밀번호 : testpass, URL
	db, _  := gorm.Open("mysql", "testuser:testpass@tcp(127.0.0.1:3306)/")
	defer db.Close() // 스콥(scope)을 벗어나면, db.CLose()실행, 이 경우는 main()함수 종료시

	dbName  :=  "testdb"  // 생성할 데이터베이스 이름

	// Raw SQL로 데이터베이스 생성
	db.Exec("CREATE DATABASE IF NOT EXISTS "  + dbName)
	db.Exec("commit;")
}
```
실행 및 결과
```bash
$ go build
$ ls
main.go  tutorial
$ ./tutorial.1 
$
```
MySQL의 쿼리 로그를 통한 결과 확인
```bash
$ sudo tail -f /var/lib/mysql/mach-W650EH.log
190501 23:55:45	   52 Connect	testuser@localhost as anonymous on 
		   52 Query	commit
		   52 Quit	
190501 23:56:14	   53 Connect	testuser@localhost as anonymous on 
		   53 Query	commit
		   53 Quit	
190501 23:56:40	   54 Connect	testuser@localhost as anonymous on 
		   54 Query	CREATE DATABASE IF NOT EXISTS  testdb
		   54 Query	commit
		   54 Quit	
190501 23:56:54	   41 Query	show databases


```
- MySQL 로그파일명은 각 사용자별로 다르니, 로그 파일 설정시 확인하기 바란다.
---
## 3. 테이블 생성 및 간단 CRUD
```go
package main

import (
	_ "github.com/go-sql-driver/mysql"

	"github.com/jinzhu/gorm"
)

// 데이터베이스 스키마 Go 언어 선언
type  Product  struct {
	gorm.Model
	Code string
	Price uint
}

func  main() {
	db, err  := gorm.Open("mysql", "testuser:testpass@tcp(127.0.0.1:3306)/testdb?charset=utf8&parseTime=True&loc=Local")
	if err !=  nil {
		panic("failed to connect database")
	}
	defer db.Close()

	// 스키마를 마이그레이트(테이블 생성)
	db.AutoMigrate(&Product{})

	// Create :  레코드 삽입/추가(insert)
	db.Create(&Product{Code: "L1212", Price: 1000})

	// Read : 읽기
	var  product Product
	db.First(&product, 1) // find product with id 1
	db.First(&product, "code = ?", "L1212") // find product with code l1212

	// Update - product의  price 를 2000 으로 갱신/수정
	db.Model(&product).Update("Price", 2000)

	// Delete - product를 삭제
	db.Delete(&product)
}
```
MySQL의 쿼리 로그를 통한 결과 확인
```bash
$ sudo tail -f /var/lib/mysql/mach-W650EH.log
190502  0:12:07	   55 Connect	testuser@localhost as anonymous on testdb
		   55 Query	SET NAMES utf8
		   55 Query	SELECT DATABASE()
		   55 Prepare	SELECT count(*) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema = ? AND table_name = ?
		   55 Execute	SELECT count(*) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema = 'testdb' AND table_name = 'products'
		   55 Close stmt	
		   55 Query	CREATE TABLE `products` (`id` int unsigned AUTO_INCREMENT,`created_at` timestamp NULL,`updated_at` timestamp NULL,`deleted_at` timestamp NULL,`code` varchar(255),`price` int unsigned , PRIMARY KEY (`id`))
		   55 Query	SELECT DATABASE()
		   55 Prepare	SELECT count(*) FROM INFORMATION_SCHEMA.STATISTICS WHERE table_schema = ? AND table_name = ? AND index_name = ?
		   55 Execute	SELECT count(*) FROM INFORMATION_SCHEMA.STATISTICS WHERE table_schema = 'testdb' AND table_name = 'products' AND index_name = 'idx_products_deleted_at'
		   55 Close stmt	
		   55 Query	CREATE INDEX idx_products_deleted_at ON `products`(deleted_at)
		   55 Query	START TRANSACTION
		   55 Prepare	INSERT  INTO `products` (`created_at`,`updated_at`,`deleted_at`,`code`,`price`) VALUES (?,?,?,?,?)
		   55 Execute	INSERT  INTO `products` (`created_at`,`updated_at`,`deleted_at`,`code`,`price`) VALUES ('2019-05-02 00:12:07.153142','2019-05-02 00:12:07.153142',NULL,'L1212',1000)
		   55 Close stmt	
		   55 Query	COMMIT
		   55 Query	SELECT * FROM `products`  WHERE `products`.`deleted_at` IS NULL AND ((`products`.`id` = 1)) ORDER BY `products`.`id` ASC LIMIT 1
		   55 Prepare	SELECT * FROM `products`  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = ? AND ((code = ?)) ORDER BY `products`.`id` ASC LIMIT 1
		   55 Execute	SELECT * FROM `products`  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = 1 AND ((code = 'L1212')) ORDER BY `products`.`id` ASC LIMIT 1
		   55 Close stmt	
		   55 Query	START TRANSACTION
		   55 Prepare	UPDATE `products` SET `price` = ?, `updated_at` = ?  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = ?
		   55 Execute	UPDATE `products` SET `price` = 2000, `updated_at` = '2019-05-02 00:12:07.164554'  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = 1
		   55 Close stmt	
		   55 Query	COMMIT
		   55 Query	START TRANSACTION
		   55 Prepare	UPDATE `products` SET `deleted_at`=?  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = ?
		   55 Execute	UPDATE `products` SET `deleted_at`='2019-05-02 00:12:07.180998'  WHERE `products`.`deleted_at` IS NULL AND `products`.`id` = 1
		   55 Close stmt	
		   55 Query	COMMIT
		   55 Quit


```
mysql 콘솔에서 테이블 조회
```sql
MariaDB [testdb]> select * from products;
+----+---------------------+---------------------+---------------------+-------+-------+
| id | created_at          | updated_at          | deleted_at          | code  | price |
+----+---------------------+---------------------+---------------------+-------+-------+
|  1 | 2019-05-02 00:12:07 | 2019-05-02 00:12:07 | 2019-05-02 00:12:07 | L1212 |  2000 |
+----+---------------------+---------------------+---------------------+-------+-------+
1 row in set (0.00 sec)

MariaDB [testdb]>
```
- 전부 삭제했으나, 레코드가 여전히 존재하는 것처럼 보인다.
- gORM은 실제 데이터를 삭제하지는 않고, 삭제 시간을 갱신해서 입력한다. 즉, deleted_at이 null 값이 아니라면, 데이터는 삭제된 것이다.
- 이는 성능을 고려한 구현방법이다.
