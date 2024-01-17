---
layout: post
title:  "쿼리 튜닝!"
date:   2024-01-15 23:00:25
categories: DataBase
tags: featured
---
# Transaction

## 1. 트랜잭션이란?

- **TCL (Transaction Control Language, 트랜잭션 제어 언어) - Commit, RollBack,** SavePoint
    - **Commit** - 변경사항을 DB에 영구 저장하는 것으로 Commit 수행시 트랜잭션 과정이 종료되는 명령어
    - **RollBack** - 작업 중 문제가 발생되어 트랜잭션의 처리 과정에서 발생한 변경사항을 취소하는 명령어
    - SavePoint - 특정시점까지의 변경사항을 Commit(저장)하겠다는 명령어
- 쪼갤 수 없는 업무 처리의 최소 단위

## 2. 트랜잭션의 ACID

1. Atomicity(원자성)- 트렌잭션의 변경사항이 데이터베이스에 모두 반영되던지, 전혀 반영되지 않아야 한다.
2. Consistency(일관성) - 트랜잭션의 작업 처리 결과가 항상 일관성이 있어야 한다.
3. Isolation(격리성) - 트랜잭션은 다른 트랜잭션에 끼어들 수 없고 마찬가지로 독립적이다.(Isolation 옵션)
4. Durability(영속성) - 결과는 영구적으로 반영되어야 한다.

- 필요성
    
    <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/54e7104f-c1aa-455c-b631-ed053c490c3c" width="30%">
            
    

## 3. Spring의 @Transactional

메서드, 클래스, 인터페이스 등에 적용이 가능

- 클래스, 인터페이스 선언할 경우 - 클래스에 존재하는 모든 메서드에 클래스가 적용
- 중첩의 경우 - 클래스 메서드, 클래스, 인터페이스 순으로 우선 적용
1. 명령어 getTransaction, commit, rollBack
2. 트랜잭션 전파 속성(Transaction Propagation)
    - Option
        
        ```java
        @Transactional(propagation=Propagation.**REQUIRED**)
        ```
        
        이미 트랜잭션이 진행중일 때 추가 트랜잭션 진행을 어떻게 할지 결정하는 것
        
        <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/dac7a569-0976-4172-9e26-ce150aeee8e6" width="34%">
        - **REQUIRED**
            
            <img src="https://private-user-images.githubusercontent.com/86454635/297308485-6e814c8e-bdbc-4509-90b6-16655b196de1.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDU0Nzk2NDQsIm5iZiI6MTcwNTQ3OTM0NCwicGF0aCI6Ii84NjQ1NDYzNS8yOTczMDg0ODUtNmU4MTRjOGUtYmRiYy00NTA5LTkwYjYtMTY2NTViMTk2ZGUxLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTE3VDA4MTU0NFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTUwNzcxYTNjZWQ2YWYxNWE5NDljYjczNDhjZjZjNWExNjdiNjVmODliMzk0YzM4MjkzNDY4OGNiZDk5NWU4YWYmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.DYGReYwk9dXv0nV_G48Q3zUF4RbVBmnqx8fh_8VKCy0" width="30%">
            
            - 로직 1 실패
                
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/a130dfb8-6360-4179-9282-c9b8f37eda77" width="30%">
                
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/d8bc5f78-8608-43a5-91f6-f4cec4bef4be "width="30%>
                
            - 로직 2 실패
                
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/8d19f5d8-906d-442c-889e-c9fe75d355a1" width="30%">
                
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/a27e8db8-667d-4588-861c-15e411b73cdb" width="30%">
                
        - **REQUIRES_NEW**
            
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/6e814c8e-bdbc-4509-90b6-16655b196de1" width="30%">
            
            - 필요성
                
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/6da56986-4290-40e4-ab6a-39d6defe5d5a" width="30%">
                
        - **NESTED**
            
            <img src="https://github.com/aswooo/aswooo.github.io/assets/86454635/52bbf90f-2470-4c7d-90d1-e31c99ed703e" width="10%">

            
            중첩 트랜잭션은 부모 트랜잭션의 영향을 받지만, 중첩 트랜잭션이 부모에 영향을 주지는 않음
            JDBC의 savepoint 기능을 사용하는데 JPA는 사용이 불가
            
        
3. 읽기(Read Only)
    - 읽기 전용 트랜잭션 생성
        
        ```java
        @Transactional(readOnly = true) // default = false
        ```
        
        - 등록, 수정, 삭제 불가
        - 예상치 못한 엔티티의 변경, 삭제를 예방
        - 성능 최적화
4. 격리 수준(Isolation)
    - Option
        
        ```java
        @Transactional(isolation=Isolation.READ_UNCOMMITTED)
        ```
        
        READ_UNCOMMITTED : 커밋되지 않은 읽기
        
        READ_COMMITTED : 커밋된 읽기(Default)
        
        REPEATABLE_READ : 반복 가능한 읽기
        
        SERIALIZABLE : 직렬화
        
5. 타임 아웃
    - 지정한 시간(S) 내에 해당 메소드 수행이 완료되지 않을 경우 rollback
        
        ```java
        @Transactional(timeout=10)
        ```
        
6. 롤백
    - 롤백 예외 설정 및 롤백 설정
        1. rollbackFor - 기본 정책에 추가로 롤백할 예외를 지정한다.
        
        ```java
        @Transactional(rollbackFor = Exception.class)
        ```
        
        2. noRollbackFor - 롤백을 하지 않을 예외를 지정
        
        ```java
        @Transactional(noRollbackFor=RuntimeException.class)
        ```
        
    
    [[Spring] 스프링의 트랜잭션 전파 속성(Transaction propagation) 완벽하게 이해하기](https://mangkyu.tistory.com/269)
    
    [[Spring] 스프링의 트랜잭션 전파 속성(Transaction propagation) 완벽하게 이해하기](https://mangkyu.tistory.com/269)
    
    [[10분 테코톡] 🐤 샐리의 트랜잭션](https://youtu.be/aX9c7z9l_u8?si=fQrFw4UzXcVNgIO1)