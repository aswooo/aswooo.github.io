---
layout: post
title:  "Redis를 이용한 랭킹 기능 구현"
date:   2022-02-25 14:34:25
categories: DataBase
tags: featured
---

# 1. 왜 Redis를 사용할까?
랭킹을 만드는데에 있어서 Redis를 사용하는 이유는 다음과 같다.

#### 1. NoSQL이며 인메모리 DB이다.
>
+ RDBMS를 사용한다면 SELECT 쿼리로 조회하고 정렬함에 있어서 많은 자원과 시간을 소모할 것이다.
+ NoSQL은 많은 양의 데이터를 효율적으로 처리가 가능하다.
+ 인메모리 DB이기 때문에 데이터 처리 속도가 빠르다.

####  2. 중복을 해결한다.
> 
+ 한 사람이 1등이고, 2등일 수 없다. 
 
####  3. Sorted Set을 지원한다.
>
+ score에 따라 자동적으로 value를 sort 한다. 추가적으로 sort를 하면서 자원을 소모하지 않아도 된다.



# 2. Sorted Set을 알아보자.



### 1. Sorted Set의 구조

![](https://images.velog.io/images/aswooo/post/8ffe84bf-f07b-4182-9339-f0c53c93e34e/image.png)

Sorted Sets는 Set과 Hash가 합쳐진 모양이다.

+ 하나의 key를 가지고 있으며, 여러 개의 score와 value의 쌍들으로 구성된다.
+ score는 이중 64비트 부동 소수점 숫자를 사용하여 점수를 저장한다.

#### - score 중복시
>score가 같으면 value로 sort된다.

### 2. Sorted Set의 명령어

#### 1. ZADD 
```
ZADD [KEY] [score1] [value1] [score2] [value2] ...
```
 KEY에 score1인 value1과 score2인 ELEMENT2를 넣는다. 더 많은 쌍 추가가 가능하다.
 ![](https://images.velog.io/images/aswooo/post/155054c2-08f1-4ec3-ac76-551c82b6a80a/image.png)
 
#### 2. ZRANGE
```
ZRANGE [KEY] [START_INDEX] [END_INDEX] [WITHSCORE]
```
START_INDEX부터 END_INDEX 까지의 Value 값을 반환한다. WITHSCORE 옵션을 추가하면  SCORE도 함께 반환된다.
>
역순의 경우 _ZREVRAGNE_ 를 사용한다.

![](https://images.velog.io/images/aswooo/post/1816071d-b82f-4cd5-b8d1-9c9218c6ac1e/image.png)

![](https://images.velog.io/images/aswooo/post/8c135be0-0448-4619-b390-a115602bee45/image.png)

#### 3. ZINCRBY 
```
ZINCRBY [KEY] [NUMBER] [Value]
```
KEY에서 Value의 SCORE를 NUMBER만큼 증가시킨다.
랭킹에 따른 점수를 수정할 때 사용할 예정이다.

추가적인 다양한 명령어는 이 [블로그](https://minholee93.tistory.com/entry/Redis-Sorted-Set)에 자세히 설명되어 있다.


 