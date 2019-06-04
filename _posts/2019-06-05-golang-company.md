---
title: golang usage
key: 20190605
tags: golang go usage  
mermaid: true
---

# Go를 사용하는 유명(?) 회사
> Go언어가 여러 분야에 사용되고 있지만, 그 "여러..."에 관한 글이 별로 보이지 않는다?
> - 인터넷에서 이것 저것 찾아서 목록을 작성해 본다.
## Go사용회사 목록
 1. Uber
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

### 1) Uber
> Uber has written over a hundred services in Golang. Geobase one of the most recent Uber services written in Golang. It matches riders to drivers, sharding the matching across machines. In fact, Golang powers a majority of high QPS services at Uber. Uber datastore schemaless sharding layer was in Python. However as their business grew, so did their resource utilization and latencies. To keep Schemaless performant, they needed a solution that would execute well at scale. Thus they rewrote Schemaless’ fleet of Python worker nodes in Go.
### 2) Google 
> Google uses Go for many internal projects. The service that runs dl.google.com--the source for Chrome, Earth, Android SDK, and other large Google downloads--has been rewritten in Go. Several of Google’s big user-facing services including youtube.com as well as golang.org also use it. There are many small projects in Google which are developed using Google App Engine's native support for Go.

### 3) Dailymotion
> It is a video-sharing website hosted in France. The website resembles YouTube; it streams videos, including original content created by Dailymotion studios. [Dailymotion](https://www.meetup.com/NYC-Women-Who-Go/events/254560843/) uses Golang for automation of APIs and End to End test cases. They conduct hundreds of automation tests every month using Golang. They chose Golang for huge loads of automation testing as it is fast and powerful. They have developed most of their backend projects in Golang because of its performance, static type checking, and simplicity.
 

### 4)  Twitch
> It is a live streaming video platform that focuses on video game live streaming. It is a subsidiary of Amazon. It also streams music broadcasts, creative content, eSports competitions and more. The Transcode system of Twitch takes the RTMP stream from the broadcaster and transcodes it into multiple HLS streams. This is implemented via a combination of Golang and C++. The distribution function that is meant to provide highest quality video streaming experience is also written in Golang.

### 5)  Fabric
> Answer the analytics engine that powers the [Fabric](https://blog.twitter.com/engineering/en_us/a/2015/handling-five-billion-sessions-a-day-in-real-time.html) Platform is built in Golang. Fabric provides users with a real-time understanding of their app's performance and health. Answer receives about five billion analytics sessions per day. Millions of devices send millions of events every second to Answers endpoint. Since its communication protocol handles compressed payloads every second they needed a service that is highly scalable. Thus for easy linear scaling and simplicity of code this entire service is written in Golang.
- 

### 6)  Sendgrid
> It is a cloud-based email service to reliably deliver high volume emails on behalf of some of the biggest companies in the world which includes uber, Airbnb, Spotify and more. SendGrid’s customizable, reliable, and scalable APIs have been developed using Golang. Such email applications can deliver the most relevant content to the right user at the right time.
- 

### 7) Medium
> The online content publishing platform Medium is also using Golang extensively. Their Neo4j database is managed by a service written in Go which they refer to as GoSocial. Medium further uses Golang for image processing as well as certain other backend services.
- 
### 8) etc
> Faceboook
> Twitter
> YouTube
> Apple
> Dropbox
> Soundcloud
> Mozilla Firefox
> The New York Times
> Github
> GOV.UK
> 
