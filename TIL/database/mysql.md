## mysql x innodb 트랜잭션 모델
- SQL 표준에 정의된 4가지 트랜잭션 격리 수준 중 REPEATABLE READ을 기본값으로 채택
- REPEATABLE READ을 위해 아래와 같이 동작
  - Locking을 이용하지 않는 consistent read 방식 사용
  - 명시적으로 Locking Read를 할 수 있도록 For update, For share 구문 제공

### consistent read (= consistent nonlocking read)
- 동시에 실행 중인 트랜잭션에서 데이터를 변경하더라도,
- 특정 시점의 스냅샷을 이용하여
- 기존과 동일한 결과를 리턴해줄 수 있는 기능

- 장점
  - lock 을 사용하지 않고 snapshot을 이용하기 때문에 동시성이 높다.
- 단점
  - 트랜잭션 제약조건과 consistent read의 제약조건이 결합되며 문제가 생긴다.
    - 트랜잭션은 한 트랜잭션 내에서 수행된 INSERT, UPDATE, DELETE 결과가 한 트랜잭션 내에서는 항상 보여야 한다.
    - consistent read는 트랜잭션 내에서 SELECT를 하면 다른 트랜잭션에서 수정을 하더라도 항상 동일한 결과를 리턴한다.


    ```
    t1 --- (1) select ----------------- (3) select ------ (4) update---- (5) select --
    t2 ----------------- (2) insert --- commit

    (1) SELECT * FROM my_table;
        return 0 rows
    (2) INSERT INTO my_table VALUES (1, 'a');
    (3) SELECT * FROM my_table;
        return 0 rows
    (4) UPDATE my_table SET value='b' WHERE id = 1;
    (5) SELECT * FROM my_table;
        return 1 rows (BOMB!)
    ```
    - (4) update 에 의해 id=1 row는 스냅샷에서 데이터를 불러오는 것이 아닌 최신 데이터를 불러온다.
    - 따라서 동일한 select 쿼리임에도 불구하고 결과가 다르게 나오는 REPEATABLE READ가 깨지는 현상이 발생한다.
  - DDL 이후에는 consistent read가 동작하지 않는다.
    - ex) DROP TABLE, ALTER TABLE

## locking read
- FOR UPDATE
  - lock에 의해 해당 row는 다른 트랜잭션에서 배타적 읽기, 수정 불가능
  - mysql innodb 엔진의 경우 consistent read는 허용됨
  - 기본적으로 consistent read를 하기 때문에 읽기가 가능한 것으로 보일 수 있음
- FOR SHARE
  - lock에 의해 해당 row는 다른 트랜잭션에서 수정 불가능
  - 다른 트랜잭션이 읽어가는 것은 허용

### Lock 의 종류: 적용요소에 따른 분류
- Shared lock (S)
  - Row-level lock
  - SELECT 위한 read lock
  - Shared lock이 걸려있는 동안 다른 트랜잭션이 해당 row에 대해 Exclusive lock 획득은 불가능하나 Shared lock 획득은 가능
  - 한 row에 대해 여러 트랜잭션이 동시에 Shared lock 획득 가능
- Exclusive lock (X)
  - Row-level lock 
  - UPDATE, DELETE를 위한 lock
  - Exclusive lock이 걸려있는 동안 다른 트랜잭션이 Exclusive lock, Shared lock 획득 불가능
- Intention lock (I)
  - Table-level lock
  - 테이블의 특정 row에 lock이 걸릴 것이라는 것을 알려주기 위해 미리 table에 걸어두는데 사용 

- https://www.letmecompile.com/mysql-innodb-lock-deadlock/ (WIP)


참고
---
- https://www.letmecompile.com/mysql-innodb-transaction-model/
