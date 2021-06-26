# Index

## index?

- 색인, DB가 데이터를 빠르게 찾을 수 있도록 컬럼의 값과 해당 레코드가 저장된 주소를 쌍으로 인덱스를 만들어둔다.
- Index가 있으면 FULL SCAN을 해서 조회해오던 것을 O(log N) 으로 가져올 수 있게된다.

## 자료구조

- B-Tree
    - 일반적으로 사용되는 알고리즘
    - 컬럼의 값을 변형하지 않고 원래의 값을 이용해 트리구조로 인덱싱한다.
    - 디스크 I/O를 고려하여 깊이를 줄일 수 있는 B-Tree 구조 이용
    - 한 쪽으로 데이터가 몰리지 않아서 최악의 경우에도 검색이 빠르다.
    - Root Block - Branch Block - Leaf Block 으로 계층이 나뉜다.
    - key와 관련된 정보를 node에 저장한다. 리프 노드를 읽기 전 원하는 값을 찾을 수 있다.
    - 탐색 과정
        - root block을 찾는다.
        - 찾는 값이 branch block에서 가장 왼쪽 값보다 작으면 왼쪽 포인터, 사이에 있으면 중간 포인터, 더 크면 오른쪽 포인터로 찾아간다.
        - 이 과정을 통해 leaf block을 찾고 그 안에서 찾고자 하는 값이 있는지 확인한다.
    - 인덱스 추가시
      - 리프노드가 자리가 부족한 경우 루트 노드의 변경까지 이어질 수 있다.
    - 인덱스 삭제시
      - 삭제되었다고 마크해둔다.
    - 인덱스 수정시
      - 추가와 삭제 과정을 진행한다.
    - 
- Hash
    - 컬럼의 값으로 해시 값을 계산해서 인덱스
    - 매우 빠른 검색속도
    - 값을 변형해서 인덱싱하므로 값의 일부만으로 검색 할 때는 hash를 사용할 수 없다.
    - 부등호 연산의 경우 문제가 발생한다.

## Clustered Index & Non-Clustered Index

![clustered_index.png](/static/TIL/database/clustered_index.png)

### Clustered Index

- 인덱스를 걸었을 때 가장 효율적일 것 같은 컬럼을 clustered index로 지정
- 테이블 생성시 자동으로 PK를 기준으로 생성됨
- 비슷한 값들을 물리적으로 인접한 장소에 저장되어 있는 데이터
    - 데이터 삽입, 수정, 삭제시 재배열 해줌
- 테이블 당 1개만 생성 가능
- 검색 속도는 non-clustered index보다 빠르지만 삽입, 수정, 삭제는 더 느리다.
  - 값이 변경되면 레코드의 물리적인 위치도 변경이 되어야 함


### Non-Clustered Index

- 테이블당 여러개 생성 가능
- 테이블의 데이터를 지정된 컬럼에 대해 물리적으로 재배열 안 함
    - 단지 지정된 컬럼에 대해 정렬시킨 인덱스를 만듦 (테이블의 데이터는 그대로)
- 검색 속도는 clustered index보다 느리지만 삽입, 수정, 삭제는 더 빠르다.


## Composite Index
- title, author에 index를 주고 title로 검색하는 것은 인덱스를 탈 수 있으나, author로 검색하는 것은 index를 타지 않게 된다.
  - [composite_index](/static/TIL/database/composite_index.webp)
  - 데이터 내부적으로 main index를 저장하고, secondary, tertiary index를 pointer로 가리키고 있음
  - 따라서 (name, age, address)로 인덱스가 걸려 있을 때에, (name) 혹은 (name, age) 혹은 (name, age, address)로는 검색할 수 있으나 age, address 만으로는 인덱스를 태울 수 없다.
  - main index에 접근하지 않으면 해당 인덱스에 접근할 수 없기 때문이다.
- 어떻게 쿼리할 것인지에 따라서 index를 잘 세워야 한다.

![composite_index.png](./static/TIL/database/composite_index.png)

- index와 동일하게 정렬된 테이블의 포인터를 저장
- composite index는 추가로 다른 컬럼의 정렬된 포인터도 저장
- 데이터를 더 빠르게 이동할 수 있기 때문에 일반 index보다 더 많이 쿼리 시간을 줄일 수 있다.
- 최대 32개의 컬럼의 데이터를 저장하는 인덱스
- 인덱스 구조로 인해 인덱스를 거는 컬럼의 순서가 매우 중요하다.
- 위 이미지에서 year, make, model 순으로 composite index를 생성하면, year index는 make에 대한 주소를, make index는 model에 대한 주소를 가지고 있게 된다.
- 이러한 포인터 순서 때문에 보조 인덱스에 참조하고 위해서는 주 인덱스를 통해 수행해야한다.
- 즉 year, (year, make), (year, make, model)로만 인덱스를 이용하여 쿼리할 수 있다.
- 카디널리티가 높은순에서 낮은 순으로 정렬하는 것이 유리하다.
    - 카디널리티: 해당 컬럼의 중복된 수치, 높을수록 겹치지 않는다.
    - 카디널리티가 높을 수록 더 많은 데이터를 거를 수 있기 때문에 카디널리티가 높은 컬럼에 인덱스를 걸어야한다.

## Boolean Index
PostgreSQL은 그 방법이 더 저렴하다고 판단될 때만 인덱스를 사용한다. 오직 2가지의 값밖에 가지지 못하는 boolean 컬럼의 인덱스는 거의 사용되지 않을 것이다. 테이블의 높은 비율이 검색되어야 하는 경우 인덱스와 테이블에 랜덤 I/O를 사용하는 것보다 전체 테이블을 순차적으로 읽는 것이 더 저렴하기 때문이다.

테이블의 더 적은 부분을 차지하는 값이 True라면 True인 경우에만 partial index를 걸어주면 된다.


## GIN (Generalized Inverted Index)
- B-tree 인덱스는 컬럼의 값을 변형하지 않고 그대로 사용
- 따라서 일치 비교의 경우는 효과적이나, `like '%keyword%'` 연산과 같이 포함여부를 확인하는 것에는 적용되기 어렵다.
- GIN의 경우 인덱스를 적용하는 컬럼의 값을 일정한 규칙에 따라 쪼개고 이 요소들을 사용한다.
    - array_ops, tsvector_ops, jsonb_ops, jsonb_path_ops 등 여러가지 기준으로 쪼갤 수 있다.
- GIN Index만 걸려있는 컬럼에 `=` 연산을 하는 경우 Full Scan이 일어나 효과가 없으나 `like '%keyword%'` 연산의 경우 효과적으로 동작

## Elasticsearch Inverted Index
![string_match.png](/static/TIL/database/string_match.png)
- RDBMS에서 문자열 검색시 like 검색을 이용하면 row 안의 내용을 모두 읽어 포함되는 여부를 확인해야 하기 때문에 속도가 느리다.

![es_inverted_index.png](/static/TIL/database/es_inverted_index.png)

- 문자열을 쪼갠 후 각 키워드들이 포함된 도큐먼트 id를 가지고 있는다.
- 데이터가 늘어나도 찾아야 하는 행이 늘어나는 것이 아니라 id의 배열값이 늘어나는 것이기 때문에 빠른 속도를 유지할 수 있다.

## 성능

인덱스 생성시 쓰기 작업에 별도의 과정이 추가된다.
따라서 인덱스가 많다고 해서 빨라지는 것은 아니다.
필요한 것에 전략적으로 인덱스를 생성해야 한다.

- insert 경우 인덱스에 대한 데이터를 추가해줘야 하고
- delete는 인덱스에 존재하는 값은 삭제하지 않고 사용하지 않는다는 표시를 한다.
    - row 수는 그대로이기 때문에 허수 데이터가 많이 존재할 수 있음
- update의 경우 이전 데이터가 삭제되고 그 자리에 새 데이터가 들어오는 것이기 때문에 insert와 delete의 문제점을 모두 가지고 있다.
- index 를 저장하기 위한 저장공간도 소모된다.


# Join
- 두 개 이상의 테이블을 하나의 집합으로 만드는 연산

## Nested Loop Join
- 반복문과 유사한 형태
- 반복문 외부에 있는 테이블을 선행 테이블 (Outer table), 내부에 있는 테이블을 후행 테이블 (Inner table)
- 선행테이블의 row 수 만큼 join이 수행된다.
    - row가 더 적은 테이블은 선행으로 두고, 인덱스가 걸려있는 테이블을 후행으로 두어 최적화 할 수 있다.
- 선행 테이블의 row가 10개이고 후행 테이블의 row가 1억개이면 후행 테이블을 10억번을 읽어야한다.
    - 후행 테이블의 초반에 조건에 일치하는 데이터를 찾았다고 하더라도 1개만 있을거라는 보장이 없어서 모두 읽어야한다.
    - 인덱스와 함께 사용이 되어야 한다.

## Sort-Merge Join
- 인덱스가 없는 경우 빨리 해당 데이터를 찾아 출력하기 위한 방법 중 하나
- 데이터들을 Sort 한 후에 Merge하여 데이터를 찾는다.
- 그러나 sort 할 때 시간이 너무 오래걸리기 때문에 RBO가 아닌 CBO에서는 Hash join을 이용한다.
    - RBO: rule based optimization, 미리 정해진 규칙에 의한 실행 계획
    - CBO: cost based optimization, 통계정보와 I/O, CPU 비용을 계산하여 실행계획을 예측

## Hash Join
- CBO 에서만 가능한 방식
- 두 개의 테이블에 인덱스가 없는 상황에서 join 하는 경우
    - 두 테이블 중 범위가 좁을 테이블을 메모리로 가져온다.
    - join 조건 칼럼의 데이터를 hash 함수에 넣어서 나온 hash value를 Hash table에 저장한다.
    - 후행 테이블의 join 조건을 hash 함수에 넣어서 나온 hash value를 선행 테이블의 hash table 값과 비교하여 같은 값이 있으면 해당 칼럼의 값을 매칭

### Sort-Merge vs Hash Join
- 둘 다 모든 테이블을 읽는다.
- Sort-Merge Join은 정렬해서 작업을 수행하지만, Hash Join은 정렬을 하지 않는다.
- 테이블 특징마다 다르지만 일반적으로 Hash Join이 Sort-Merge 보다 2배 이상 성능이 좋다.
- 인덱스가 있더라도 대용량 데이터를 처리해야 하는 경우 Hash Join을 하는 경우도 종종있다.


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

## 트랜잭션의 격리 수준
### 필요성
- 하나의 트랜잭션이 동작 중일 때에 다른 트랜잭션이 관여할 수 없도록 막는 Locking 개념이 등장
- 무조건적인 Locking은 많은 트랜잭션들을 순차적으로 처리하게 되며 DB 성능을 떨어뜨림
- 반대로 응답성을 높이기 위해 Locking 범위를 줄이게 되면 잘못된 값이 처리될 여지가 생김
- 최대한 효율적인 locking 방법이 필요

### 격리 수준의 종류

#### READ UNCOMMITTED (Level 0)
- 각 트랜잭션의 변경 내용의 commit/rollback 여부와 관계 없이 다른 트랜잭션에서도 값을 읽을 수 있다.
- 정합성에 문제가 많아 사용하지 않는 것을 권장
- update 후 commit되지 않은 값을 다른 트랜잭션에서 읽을 수 있다.

#### READ COMMITTED (Level 1)
- 대부분의 RDB에서 기본적으로 사용하고 있는 격리 수준
    - PostgreSQL 역시 해당 격리 수준을 이용하고 있음
- Level 0의 Dirty Read와 같은 문제가 발생하지 않는다.
    - Dirty Read: 트랜잭션 작업이 완료되지 않았는데 다른 트랜잭션에서 볼 수 있게 되는 것 
- 실제 테이블 값이 아닌 Undo 영역의 백업된 레코드에서 값을 가져온다.
- 하나의 트랜잭션에서 똑같은 Select 쿼리를 했을 때 항상 같은 값을 가져와야 하는 REPEATABLE READ의 정합성에서 어긋난다.

#### REPEATABLE READ (Level 2)
- MySQL의 경우 트랜잭션마다 트랜잭션 ID를 부여하여 해당 ID보다 작은 트랜잭션 번호에서 변경된 것만 읽게 한다.
- Non-Repeatable Read가 발생하지 않음
    - Non-Repeatable Read: 한 트랜잭션에서 같은 쿼리를 두 번 수행할 때, 두 쿼리의 결과가 상이하게 나타나는 비일관성 현상
- UNDO 공간에 백업해두고 실제 레코드 값은 변경하며, 변경 전에 만들어진 트랜잭션에서는 UNDO 공간에 백업된 데이터를 가져온다.
    - 백업된 데이터는 불필요시 주기적으로 삭제하며, 백업 레코드가 많아지면 성능 저하가 찾아올 수 있다.
- 이러한 업데이트 방식을 MVCC (Mutli Version Concurrency Control)라고 부른다.

#### Serializable (Level 3)
- 가장 단순하지만 가장 엄격한 격리 수준으로 동시 처리 성능이 가장 낮다.
- Phantom Read가 발생하지 않음
    - Phantom Read: 하나의 트랜젝션에서 Update 명령이 유실되거나 덮어써지는 현상.update후 커밋하고 다시 조회했는데, 다른 값이 보이거나 데이터가 없어진 경우
- 동일한 행에 중복한 변경점이 생기면 두번째 쓰기 작업은 실패하게 된다.
  - T1 에서 200으로 변경, T2에서 300으로 변경한 상태에서 T1이 commit되면, T2는 실패

# 파티셔닝

퍼포먼스, 가용성, 정비용이성을 목적으로 논리적인 데이터를 다수의 테이블로 쪼개는 행위

## 수평 파티셔닝 (= 샤딩)

![horizontal partitioning](/static/TIL/database/horizontal_partitioning.png)

- 동일한 스키마를 가진 테이블을 여러개로 복제
- 각각의 샤드에 어떤 데이터가 저장될지를 샤드키를 기준으로 분리 한다.
- 데이터 엑세스 패턴과 균등하게 저장될 수 있을지를 고려하여 샤드키를 결정해야 한다.

## 수직 파티셔닝

![vertical partitioning](/static/TIL/database/vertical_partitioning.png)

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

![aurora](/static/TIL/database/aurora.png)

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


# MySQL vs PostgreSQL

- MySQL은 REPETABLE READ의 트랜잭션 격리 수준을 default로 이용하고 있고, PostgreSQL은 READ COMMITTED를 default로 이용하고 있다.
- MySQL은 DDL이 트랜잭션 안에서 수행되더라도 rollback 되지 않지만, PostgreSQL은 가능하다.
- PostgreSQL은 업데이트시 기존 데이터를 삭제하고 새로운 행을 추가해서 업데이트가 느리지만, MySQL은 비교적 빠르다.
- MySQL은 Nested Loop Join만 제공하지만, PostgreSQL은 Nested Loop Join, Sorted Merge Join, Hash Join을 제공한다.
- MySQL은 DDL이 non-blocking으로 동작하지만 PostgreSQL은 blocking하다.
- PostgreSQL은 데이터가 삭제되거나 업데이트 되어도 남아있기 때문에 vacuum을 통해 주기적으로 디스크를 정리해주어야 한다.

# Explicit Locking - Row-level locks

## For Update
FK가 걸려있는 테이블들도 모두 lock이 걸림
purchase 테이블과 User 테이블이 FK로 걸려있고, purchase에 `For Update`로 쿼리하게 되면 이 트랜잭션이 끝날 때까지 다른 트랜잭션은 연결되어있는 User테이블의 row도 조회할 수 없음

### For Update
- lock을 걸어서 다른 트랜잭션이 block됨
- 사용하고 있던 A 트랜잭션이 끝나야 B가 작업할 수 있음
- read, update 뿐만 아니라 select for update 구문까지 막음

### For Update No Wait
- A가 사용하고 있어도 기다리지 않고 접근
- 그러나 A가 사용하고 있다면 에러가 발생

### For Update Skip Locked
- lock 걸려 있는 것 제외하고 조회

## For Share
- For Update의 약한 형태
- lock이 걸려있는 데이터를 update, delete 할 수는 없지만, select for share를 통한 접근은 가능


## select for no key updates
- selef for update와 유사하게 동작하지만, select for share를 block하지 않음

## select for key share.
- lock clause 중에 가장 약한 것
- select for share와 비슷하게 동작
- delete는 막아주지만, key 값을 수정하지 않는 update는 허용

https://www.notion.so/yool/Selecting-for-Share-and-Update-in-PostgreSQL-398163e93abf4166b51e91be86ccc68b


## 참고 자료

- [https://velog.io/@soosungp33/CS-데이터-베이스](https://velog.io/@soosungp33/CS-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B2%A0%EC%9D%B4%EC%8A%A4)
- [https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database)
- [https://mangkyu.tistory.com/97](https://mangkyu.tistory.com/97)
- [https://nesoy.github.io/articles/2018-02/Database-Replication](https://nesoy.github.io/articles/2018-02/Database-Replication)
- [http://theeye.pe.kr/archives/1917](http://theeye.pe.kr/archives/1917)
- [https://medium.com/@sunnkis/database-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%EC%9D%98-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80%EC%9D%B4%EB%9E%80-10224b7b7c0e](https://medium.com/@sunnkis/database-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%EC%9D%98-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80%EC%9D%B4%EB%9E%80-10224b7b7c0e)
