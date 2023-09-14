---
layout: post
title:  "Docker Redis + Spring 연결!"
date:   2023-02-21 14:34:25
categories: DataBase
tags: featured
--- 
 
 이전 글처럼 redis를 도커에서 띄우고 spring을 ide 환경과 docker에서 각각 실행해서 redis와의 연결이 되었는지 확인을 해보고자 한다.

Docker는 window상에서 설치한 docker desktop for window다.

## 1. 사전 준비
### 1. 스프링 의존성 추가(gradle)

```
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```
 스프링 부트에서 공식으로 지원하는 라이브러리로 추가함으로서 spring에서 redis를 손쉽게 사용할 수 있는 starter이다. 
 

###  2. application.properties 수정

spring boot redis 프로퍼티를 추가해야한다.

```
spring.redis.host=localhost		# 레디스 서버 호스트 지정 (defaut: localhost)
spring.redis.port=6379			# 레디스 서버 포트 (defaut: 6379)
```

사실 이 두 가지는 기본값으로 들어가 있어서 지정해주지 않아도 무방하다.
추가적인 프로퍼티는 다음을 [참고](https://zetawiki.com/wiki/%EC%8A%A4%ED%94%84%EB%A7%81_REDIS_%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)하자.

#### 3. 테스트 코드 작성

본인은 MVC 패턴으로 코드를 작성해서 redis에서 값을 읽어와서 return 하는 코드를 작성했다.


## 2. 실행

### 1. Redis(docker) + Spring(IDE 실행)

![](https://images.velog.io/images/aswooo/post/72513a11-5938-4bd4-9200-cdabdd1e484e/image.png)


그냥 위에까지 작성한 것으로 충분히 실행이 가능하다.
Backend로 요청을 넣었고 해당 요청을 정상적으로 처리하는 것을 확인할 수 있다.

### 2. Redis(docker) + Spring(docker)

아마 Backend를 IDE에서 로컬로 실행하는 부분은 찾아보면 어렵지 않다.
하지만 나는 각각의 컨테이너로 실행하고 요청을 보내면 처리가 되지않는다.
```
docker redis Unable to connect to localhost:6379	# 해당 오류가 발생한다!
```

이 오류를 찾아보면 정말 많은 stackoverflow등 많은 글들이 존재한다.
하지만 그곳에 적힌 방법을 사용해도 redis와 연결이 되지 않았다.

_아직 도커에 대해서 잘 알지는 못하지만 대충 이해한 바로는 _

도커에서 컨테이너를 띄우면 각 컨네이너와 localhost는 독립적이게 된다. 
**따라서 docker에서 실행중인 서버는 localhost로 접속을 하는 것이 아닌 docker host ip를 통해서 접속을 해야 한다는 것이다.**

해결 방법은 다음과 같다.
```
spring.redis.host=host.docker.internal	# Windows, Mac에서 사용

```
~~172.17.0.1를 사용해도 된다는데 난 안된다...~~

이렇게 접속을 하면 host.docker.internal:6379로 연결을 하게 되며 redis와의 연결이 가능하다.

