---
layout: post
title:  "Redis Container의 데이터 백업 및 복원"
date:   2023-09-15 23:52:25
categories: Redis
tags: featured
---

현재 개발을 하면서 자동배포를 하고 있는 중이며, Docker Compose를 이용해서 FE, BE, Redis 컨테이너를 돌리고 있는 상황이다.
Redis는 인메모리 DB인 탓에 데이터가 저장되지 않고 사라지기 때문에 배포를 할 경우에 데이터가 사라지는 문제점이 있다. 


## 1. Docker Volume 설정
> Docker-Compose.yml

```
redis:
      image: redis:alpine
      command: redis-server --port 6379
      container_name: redis
      hostname: redis
      networks:
        - app-tier
      ports:
        - 6379:6379
      volumes:
      - ./redis:/data
 ```
* docker폴더의 최상위 루트의 `redis 디렉토리`와 redis 컨테이너의 `/data 디렉토리`와 마운트했다.
 /data 디렉토리는 redis의 스냅샷을 저장할 경우 저장되는 경로로 `redis.config`의 dir 항목을 통해서 확인할 수 있다.
 
## 2. Redis의 스냅샷 저장
 
 redis의 데이터 저장의 경우는 두가지가 있다. 
 간단하게 이해하고 추후에 자세히 알아봐야겠다.
 
####  1. RDB
* 특정한 시점에 대한 데이터 스냅샷을 저장하는 것이다.
####  2. AOF
* 변경된 데이터에 대한 로그를 저장하는 것이다.

현재 상황은 재배포를 하면서 배포를 하는 시점에 대한 데이터가 필요하기 때문에 RDB방식을 사용했다. 

### 2-1. snapshot 저장방법
간단하다.

 ```
save
```
 위 명령을 통해서 명령어 입력 시점에 대한 데이터의 snapshot을 남긴다.
 
 
 ## 3. Snapshot의 복원
 
 현재 컨테이너 외부에 볼륨이 마운트되어 있기 때문에 Redis 컨테이너 입장에서는 `/data`에 항상 snapshot이 있는 상황이다.
 해당 디렉토리에 파일이 있을 경우 redis가 실행이 되면서 알아서 읽어온다.
 이는 Redis가 디폴트로 RDB로 설정이 되어있기 때문으로 보인다.