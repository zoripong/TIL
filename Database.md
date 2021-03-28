# Index

## index?

- 색인, DB가 데이터를 빠르게 찾을 수 있도록 컬럼의 값과 해당 레코드가 저장된 주소를 쌍으로 인덱스를 만들어둔다.
- DBMS는 항상 정렬된 상태를 유지한다.

## 자료구조

- B-Tree
    - 일반적으로 사용되는 알고리즘
    - 컬럼의 값을 변형하지 않고 원래의 값을 이용해 트리구조로 인덱싱한다.
- Hash
    - 컬럼의 값으로 해시 값을 계산해서 인덱스
    - 매우 빠른 검색속도
    - 값을 변형해서 인덱싱하므로 값의 일부만으로 검색 할 때는 hash를 사용할 수 없다.
    - 부등호 연산의 경우 문제가 발생한다.

## 성능

인덱스 생성시 쓰기 작업에 별도의 과정이 추가된다.

- insert/update의 경우 인덱스에 대한 데이터를 추가해줘야 하고
- delete는 인덱스에 존재하는 값은 삭제하지 않고 사용하지 않는다는 표시를 한다.
    - row 수는 그대로이기 때문에 허수 데이터가 많이 존재할 수 있음

# 정규화

## 정규화란

- 데이터베이스에서 중복을 최소화하기 위해 데이터를 구조화하는 작업
- 각 정규형에 따라 만족하지 못하는 릴레이션을 나누어서 만족도록 분해하는 작업

제 1정규형, 제 2정규형, 제 3정규형, BCNF 정규형이 있다.

## 장점

- 데이터베이스에서 이상현상을 방지할 수 있다.
    - 삽입 이상
        - 원하지 않는 자료가 삽입되거나
        - 삽입하는데 자료가 부족해서 삽입이 안되거나
    - 삭제 이상
        - 원하지 않는 자료가 삭제되거나
    - 갱신 이상
        - 정합성이 떨어지게 되거나

## 단점

- join 연산이 릴레이션간에 많아진다.
    - 역정규화를 통해 해결할 수 있다.

# 트랜잭션

## 트랜잭션이란?

작업의 완전성을 보장해주는 것

논리적인 작업 셋을 모두 완벽하게 처리하지 못한경우 원 상태로 복구하여 일부만 적용되지 않도록 한다.

DB 커넥션 개수가 제한적이기 때문에, 트랜잭션의 범위는 최소한으로 가져가야한다.

### ACID 특성

- Atomicity (원자성): 한 트랜잭션은 하나로 동작해야한다. 수행되거나, 실패하거나
- Consistent (일관성): 트랜잭션이 완료된 다음에도 일어나기 전과 동일하게 데이터 일관성을 보장
- Isolation (고립성): 각 트랜잭션은 서로 간섭없이 독립적으로 수행
- Durability (지속성): 트랜잭션이 정상적으로 종료된 후 영구적으로 DB에 결과가 저장되어야 함

# 파티셔닝

퍼포먼스, 가용성, 정비용이성을 목적으로 논리적인 데이터를 다수의 테이블로 쪼개는 행위

## 수평 파티셔닝 (= 샤딩)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d4875a0-c6f3-49a1-a9d5-aa32f6b8f73a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d4875a0-c6f3-49a1-a9d5-aa32f6b8f73a/Untitled.png)

- 동일한 스키마를 가진 테이블을 여러개로 복제
- 각각의 샤드에 어떤 데이터가 저장될지를 샤드키를 기준으로 분리 한다.
- 데이터 엑세스 패턴과 균등하게 저장될 수 있을지를 고려하여 샤드키를 결정해야 한다.

## 수직 파티셔닝

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/259ab1a5-c397-417a-8628-cb4353416d02/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/259ab1a5-c397-417a-8628-cb4353416d02/Untitled.png)

- 하나의 테이블에 저장된 데이터들을 여러 테이블로 분리하는 것

# replication

두 개 이상의 DBMS 시스템을 Master - Slave로 나눠서 동일한 데이터를 저장하는 방식

Master는 데이터의 수정사항을 반영만하고, Replication을 하여 Slave DBMS에 실제 데이터를 복사

Slave는 조회용으로, Master는 쓰기용으로 사용하며 부하를 분산시킨다.

비동기 방식으로 운영되어서 지연 시간이 거의 없다. 그러나 동기화가 보장되지 않아 일관성있는 데이터를 얻지 못할 수 있다.

수직적으로 구축

### 과정

- master 노드에 쓰기 트랜잭션 수행
- master 노드는 데이터를 저장하고 트랜잭션에 대한 로그를 파일에 기록 (binlog)
- slave 노드의 IO Thread는 master 노드의 로그 파일 (binlog)를 파일(replay log)에 복사
- slave 노드의 SQL Thread는 파일 (replay log)를 한 줄씩 읽으며 데이터를 저장

# 클러스터링

여러 개의 DB를 수평적인 구조로 구축하는 방식

분산함으로써 single point of failure와 같은 문제를 해결할 수 있는 fail over 시스템을 구축할 수 있다.

동기 방식으로 노드들간의 데이터를 동기화 한다. (일관성)

여러 노드들에 동기화하는 시간이 필요해서 쓰기 성능이 떨어진다.

장애가 전파된 경우 처리가 까다롭다

데이터 동기화에 의해 스케일링에 한계가 있다.

모든 노드를 활성화 시켜두는 Active-Active와 일부는 대기상태로 구성하는 Active-Standby 구조로 나눌 수 있다. 

### 과정

- 1개의 노드에 쓰기 트랜잭션이 수행되고, commit을 실행
- 실제 디스크에 내용을 쓰기 전에 다른 노드로 데이터의 복제를 요청
- 다른 노드에서 복제 요청을 수락했다는 신호를 보내고 디스크에 쓰기 시작
- 다른 노드로부터 신호를 받으면 실제 디스크에 데이터를 저장한다.

# Aurora

## Aurora

AWS에서 mysql, postresql 을 호환해서 만든 RDBMS

## RDS vs Aurora

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d2d773e2-5b6f-45bc-b3c2-85fab1f444ba/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d2d773e2-5b6f-45bc-b3c2-85fab1f444ba/Untitled.png)

- 스토리지
    - rds의 경우 EBS로 데이터를 쌓고 미러링하여 replica에 복제
    - 오로라는 인스턴스와 스토리지 영역이 나뉘어져 있음
        - 오로라는 4/6 쿼럼을 사용해서 스토리이지에 저장, replica로 보내는 것은 frm 및 redo log
- read replica 구성
    - rds는 standby와 read replica를 만들 때 binlog를 이용하지만
    - 오로라는 내부 storage 및 redo log 전송을 통해 동기화 한다.
- 관리 주체
    - rds의 경우 mysql, postgresql의 버전을 직접 관리하지만
    - 오로라는 aws가 개발해서 버전 업그레이드를 주기적으로 해준다.

## 참고 자료

- [https://velog.io/@soosungp33/CS-데이터-베이스](https://velog.io/@soosungp33/CS-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B2%A0%EC%9D%B4%EC%8A%A4)
- [https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database)
- [https://mangkyu.tistory.com/97](https://mangkyu.tistory.com/97)
- [https://nesoy.github.io/articles/2018-02/Database-Replication](https://nesoy.github.io/articles/2018-02/Database-Replication)
- [http://theeye.pe.kr/archives/1917](http://theeye.pe.kr/archives/1917)
