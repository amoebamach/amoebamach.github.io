---
title: golang usage
key: 20190605
tags: golang go usage  
mermaid: true
---

# Go를 사용한 오픈소스 프로젝트 10선
> Go언어가 여러 분야에 사용되고 있지만, 그 "여러..."에 관한 글이 별로 보이지 않는다?
> - 인터넷에서 이것 저것 찾아서 목록을 작성해 본다.
## 오픈소스 목록
 1. Docker
 2. Kubernetes
 3. Etcd & Fleet
 4. Deis
 5. Flynn
 6. Lime
 7. Revel
 8. InfluxDB
 9. Syncthing
 10. Gogs(Go Git Service)

	- 2019.06.05. 현재 목록 

---
## 목록에 대한 간단 정보 

### 1) Docker
> - 프로젝트 : Docker
> - GitHub: https://github.com/docker/docker

```bash
Docker는 Go로 작성되었다.

```
> 왜? Docker를 Go언어로 작성했을까?
>  [슬라이드](https://www.slideshare.net/jpetazzo/docker-and-go-why-did-we-decide-to-write-docker-in-go)
### 2) Kubernetes 
> - 프로젝트: Kubernetes
> - GitHub: https://github.com/GoogleCloudPlatform/kubernetes

### 3) Etcd & Fleet
> - 프로젝트: Etcd & Fleet
> - https://github.com/coreos/etcd
 
-  CoreOS는 도커를 사용하여 리눅스를 느슨하게 결합된 컨테이너 그룹으로 전환시켰습니다.
 - CoreOS의 기본 서비스 : etcd + fleet
 - Fleet : CoreOS 클러스터를 단일 init시스템을 공유하는 것 처럼 취급
 - etcd : 분산 Key/Value 저장소, 도커 응용프로그램 및 CoreOS인스턴스 사이의 설정 동기화
 - 위 두가지 기본 서비스는 Go로 작성됨
### 4) Deis - deprecated?
> - 프로젝트: Deis
> - https://github.com/deis/deis
- openshift? 

### 5) Flynn
> - 프로젝트: Flynn
> - https://github.com/flynn/Flynn
- Deis와 유사하지만, ... PaaS
- 

### 6) Lime
> - 프로젝트: Lime
> - https://github.com/limetext/lime
- Text editor
- 

### 7) Revel
> - 프로젝트: Revel
> - https://github.com/revel/revel
- web framework
- 라우팅, 캐싱, 매개변수 구문분석, 템플릿, 국제화, 테스트프레임워크, 필터
- 
### 8) InfluxDB
> - 프로젝트: InfluxDB
> - https://github.com/influxdb/influxdb
- 외부 종속없는 분산 시계열 데이터베이스
- 
### 9) Syncthing
> - 프로젝트: Syncthing
> - https://github.com/syncthing/syncthing
- Open Source Continuous File Synchronization
- 
### 10) Go Git Service
> - 프로젝트: Gogs
> - https://github.com/gogits/gogs
- Gogs is a painless self-hosted Git service
- *x1000000000000000000000

