# Database_Tech
Database Management Theory 

Study , Interview / Anomaly 
============================

##### Anomaly
- Deletion anomaly : 투플 삭제시 같이 저장된 다른 정보까지 연쇄적으로 삭제되는 현상 -> 연쇄삭제 (triggered deletion) 문제 발생 
- Insertion anomaly : 투플 삽입 시 특정 속성에 해당하는 값이 없어 NULL 값을 입력해야 하는 현상 -> NULL 값 문제 발생 
- Update anomaly : 투플 수정 시 중복된 데이터의 일부만 수정되어 데이터의 불일치 문제가 일어나는 현상 -> 불일치 문제 발생 


##### Functional Dependancy 
- 함수 종속성 규칙 (Functional Dependancy Rule)
  - X, Y, Z가 릴레이션 R에 포함된 속성의 집합이라고 할 때, 함수 종속성에 관한 다음과 같은 규칙이 성립 
   - 부분집합 규칙 : if Y -> X, then X -> Y
   - 증가 규칙 : if X -> Y, then XZ -> YZ 
   - 이행 규칙 : if X -> Y and Y -> Z, then X -> Z
   
   - 결합 규칙 : if X -> Y and X -> Z, then X -> YZ
   - 분해 규칙 : if X -> YZ, then X -> Y and X -> Z
   - 유사이행 규칙 : if X -> Y and WY -> Z, then WX -> Z
   
   

Study , Interview / Transaction
====================
##### Transaction : DBMS 에서 데이터를 다루는 논리적인 작업의 단위
- 트랜잭션은 전체가 수행되거나 또는 전혀 수행되지 않아야 함 (all or nothing)

##### Transaction Example
<pre>
<code>
START TRANSACTION

UPDATE Customer
SET balance = balance - 1000
WHERE name='박지성'

UPDATE Customer
SET balance = balance + 10000
WHERE name='김연아'

COMMIT
</code>
</pre>

- 트랜잭션의 성질은 원자성,일관성,고립성,지속성을 가지고 있음
- 표준 명령어 : START TRANSACTION, Mysql : SET TRANSACTION NAME <이름> => 트랜잭션의 시작 
- 표준 명령어 : COMMIT, Mysql : COMMIT => 트랜잭션의 종료 
- 표준 명령어 : ROLLBACK, Mysql : ROLLBACK { TO <savepoint> } => 트랜잭션을 전체 혹은 <savepoint> 까지 무효화 시킴.
- 표준 명령어 : SAVEPOINT, Mysql : SAVEPOINT <savepoint> => <savepoint> 를 만듬.

##### ACID

1. Atomicity : 트랜잭션에 포함된 작업은 전부 수행되거나 아니면 전부 수행되지 않아야 함.
2. Consistency : 트랜잭션을 수행하기 전,후 데이터베이스는 항상 일관된 상태를 유지해야 함.
  - 트랜잭션은 데이터베이스의 일관성을 유지해야함.
  - 일관성은 테이블이 생성 시 CREATE 문과 ALTER 문의 무결성 제약조건을 통해 명시됨 
3. Isolation : 수행중인 트랜잭션에 다른 트랜잭션이 끼어들어 변경 중인 데이터 값을 훼손하는 일이 없어야 함.
  - 데이터베이스는 공유가 목적이므로 여러 트랜잭션이 동시에 수행됨
  - 동시에 수행되는 트랜잭션은 상호 존재를 모르고 독립적으로 수행되는데, 이를 고립성이라고 함.
  - 고립성을 유지하기 위해서는 트랜잭션이 변경중인 임시 데이터를 다른 트랜잭션이 읽고 쓸 때 제어가 필요함.
4. Durability : 수행을 성공적으로 완료된 트랜잭션은 변경한 데이터를 영구히 저장해야 함.
  - 트랜잭션이 정상적으로 완료 ( COMMIT ) 혹은 부분 완료 ( PARTIAL COMMIT ) 한 데이터는 DBMS 가 책임지고 데이터베이스에 기록하는 성질 
  - 부분 완료가 되면 버퍼 내용 기록후 완료 처리, 부분완료를 실패할 경우 작업을 취소한다. 
 
 
 ##### Concurrency Control
 트랜잭션 1, 트랜잭션 2, 발생 문제, 동시 접근
 ===================================
 1. 읽기, 읽기, 읽음 ( 읽기만 하면 아무 문제가 없음 ), 허용
 2. 읽기, 쓰기, (Dirty read, non-repeatable read, phantom read), 허용 혹은 불가 선택 
 
  - Dirty read 
    - 읽기 작업을 하는 트랜잭션 1이 쓰기 작업을 하는 트랜잭션 2가 작업한 중간 데이터를 읽기 때문에 생기는 문제 
    - 작업 중인 트랜잭션2가 어떤 이유에서 작업을 철회 ( ROLLBACK ) 할 경우 트랜잭션 1은 무효가 된 데이터를 읽게 되고 잘못된 결과를 도출하는 현상
    
  - Non-Repeatable read
    - 트랜잭션 1이 데이터를 읽고 트랜잭션 2가 데이터를 쓰고 (갱신, UPDATE ) 트랜잭션 1이 다시 한번 데이터를 읽을 때 생기는 문제 
    - 트랜잭션 1이 읽기 작업을 다시 한 번 반복할 경우 이전의 결과와 다른 결과가 나오는 현상 
  - Phantom read
    - 트랜잭션 1이 데이터를 읽고 트랜잭션 2가 데이터를 쓰고 (삽입) 트랜잭션 1이 다시 한번 데이터를 읽을 때 생기는 문제
    - 트랜잭션 1이 읽기 작업을 다시 한 번 반복할 경우 이전에 없던 데이터 ( 유령 데이터 ) 가 나타나는 현상 
    
 3. 쓰기, 쓰기, Lost update (절대 허용하면 안 됨), 허용 불가 (LOCK 을 이용)
  - Lost update : 두개의 트랜잭션이 한 개의 데이터를 동시에 갱신 (Update) 할 때 발생하며, 데이터베이스에서 절대 발생하면 안되는 현상
    * 두 개의 트랜잭션이 동시에 작업을 진행, 한 개의 데이터에 두개의 트랜잭션이 접근하여 갱신하는 작업 
    
 
 ##### LOST UPDATE
  - TRANSACTION T1
  <pre>
  <code>
   A = read_item(X); 
   A = A-100
   // X = 1000
  </code>
  </pre>
  
  - TRANSACTION T2
  <pre>
  <code>
   B = read_item(X);
   B = B+100
   // X = 1000
  </code>
  </pre>
  - 버퍼의 데이터 값을 보여준다. 
  <pre>
  <code>
   write_item(A -> X);  // X = 900
   write_item(B -> X);  // X = 1100
  </code>
  </pre>
  
  ##### LOST UPDATE 문제를 해결하기 위해서는 Lock 을 사용해야 함.
  - LOCK
    * Lost update 문제를 해결하려면 상대방 트랜잭션이 데이터를 사용하는지 여부를 알 수 있는 규칙이 필요함. 
    * 데이터를 수정 중이라는 사실을 알리는 방법의 잠금장치 
    
    - TRANSACTION T1
     <pre>
      <code>
      LOCK(X)
      A = read_item(X);
      A = A-100;
      // X = 1000
      </code>
      </pre>
      
    - TRANSACTION T2
     <pre>
      <code>
      LOCK(X)
      (Wait..대기)
      // X = 1000
      </code>
      </pre>
      
    - TRANSACTION T1
     <pre>
      <code>
      write_item(A->X);
      UNLOCK(X);
      // X = 900
      </code>
      </pre>
      
   - TRANSACTION T2
     <pre>
      <code>
      B = read_item(X);
      B = B+100;
      write_item(B->X);
      UNLOCK(X);
      // X = 1000
      </code>
      </pre>
     
##### LOCK TYPE 
- 공유락 (LS, Shared Lock) : 트랜잭션이 읽기를 할 때 사용하는 락
- 배타락 (LX, Exclusive Lock) : 읽고 쓰기를 할 때 사용하는 락 

- 공유락과 배타락을 사용하는 규칙
  - 데이터에 락이 걸려있지 않으면, 트랜잭션은 데이터에 락을 걸 수 있다. 
  - 트랜잭션이 데이터 X를 읽기만 할 경우 LS(X) 를 요청하고, 읽거나 쓰기를 할 경우 LX(X)를 요청한다.
  - 다른 트랜잭션이 데이터에 LS(X)을 걸어둔 경우, LS(X)의 요청은 허용하고 LX(X)는 허용하지 않는다.
  - 다른 트랜잭션이 데이터에 LX(X)을 걸어둔 경우, LS(X)와 LX(X)모두 허용하지 않는다.
  - 트랜잭션이 락을 허용받지 못하면 대기 상태가 된다. 
  
- 2 Phase Locking
  - 락을 걸고 해제하는 시점에 제한을 두지 않으면 두 개의 트랜잭션이 동시에 실행될 때 데이터의 일관성이 깨질 수 있어 이를 방지하는 방법
  - 확장단계 ( Growing phase, Expanding phase ) => 트랜잭션이 필요한 락을 획득하는 단계로 이 단계에서는 이미 획득한 락을 해제하지 않음
  - 수축단계 ( Shrinking phase ) => 트랜잭션이 락을 해제하는 단계로, 이 단계에서는 새로운 락을 획득하지 않음
  <pre>
  <code>
      [작업설명]두 개의 데이터에 두 개의 트랜잭션이 접근하여 갱신하는 작업 
      [문제발생]락을 사용하되 2단계 락킹 기법을 사용하지 않을 경우
      [문제해결]2단계 락킹 기법을 사용할 경우  
   </code>
   </pre>

##### DeadLock
- 두 개 이상의 트랜잭션이 각각 자신의 데이터에 대하여 락을 획득하고 상대방 데이터에 대해 락을 요청하면 무한 대기 상태에 빠질 수 있는 현상으로, 교착상태라고도 함.
  - 데드락 해결
   - 일반적으로 데드락이 발생하면 DBMS 는 T1 혹은 T2 의 작업 중 하나를 강제로 중지 시킴 
   - 그 결과 나머지 트랜잭션은 정상적으로 실행됨
   - 이때 중지시키는 트랜잭션에서 변경한 데이터는 원래 상태로 되돌려 놓음 
   
