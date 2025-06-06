# 동시성 문제 해결

### 동시성 문제란 무엇이며, 이를 해결하기 위한 기본적인 전략을 설명해주세요. 실제 운영 환경에 적용한 사례가 있다면 함께 설명해 주어도 좋습니다.
힌트: 동시성은 다중 사용자/요청이 동시에 자원에 접근할 때 발생합니다. 락(lock), 큐(queue), 트랜잭션 격리 수준 등의 개념을 활용해 본 경험을 떠올려 보세요.

---

## 🌱 동시성 문제
여러 작업(사용자, 프로그램, 요청 등)이 동시에 **같은 자원**(데이터, 메모리 등)을 사용하려고 할 때 생기는 오류나 충돌 문제

### ⚠️ 대표적인 동시성 문제: 
1. **경쟁 상태 (Race Condition)** : 작업 순서에 따라 결과가 달라지는 현상.
   > 예: 두 명이 동시에 같은 계좌에 출금 요청 → 잔액이 꼬이는 문제 발생
2. **데이터 불일치** : 동시에 수정된 결과가 꼬여 잘못된 결과가 저장되는 문제.
   > 재고가 1개인 물건을 두 명이 동시에 구매 → 재고가 -1이 되는 현상
3. **교착상태 (Deadlock)** : 서로 자원을 점유한 채 상대가 가진 자원을 기다리면서 아무 작업도 진행되지 않는 상태.
   > A는 B의 자원을, B는 A의 자원을 기다리는 상황
4. **기아 상태 (Starvation)** : 특정 작업이 계속 자원을 얻지 못해 실행되지 못하는 상태.
   > 우선순위가 낮은 작업이 무한히 대기하게 됨
<br/>

### 🧑🏻‍🌾 동시성 문제 해결 전략
1. **락 Lock** : 한 번에 하나의 작업만 자원에 접근할 수 있도록 제한하는 방법 <br/>
   > 예: 은행 계좌의 잔액을 수정할 때 Lock을 걸어 한 스레드만 접근하도록 처리
2. **세마포어 Semaphore** : 동시에 접근 가능한 작업 수를 제한하는 방법 <br/>
   > 예: 데이터베이스 연결 풀에서 동시에 최대 N개의 연결만 허용
3. **모니터 Monitor** : 락과 조건(신호)을 조합한 고급 동기화 도구 (주로 Java 등에서 사용)
4. **메시지 큐 Message Queue** : 자원을 직접 공유하지 않고, 메시지를 주고받으며 비동기적으로 처리 <br/>
   > Kafka, RabbitMQ 등을 통해 주문 → 결제 → 재고 차감 등을 순차적으로 처리
5. **트랜잭션 격리 수준** Transaction Isolation Level : 여러 트랜잭션이 동시에 실행될 때 데이터 충돌을 방지하는 DB 설정 <br/>

   | 격리 수준 (Isolation Level) | 설명 |
   |-----------------------------|------|
   | **Read Uncommitted**        | 가장 낮은 보호 수준. 아직 완료되지 않은(커밋되지 않은) 데이터도 읽을 수 있음 |
   | **Read Committed**          | 커밋된 데이터만 읽음. Dirty Read는 방지되지만 Non-repeatable Read는 가능 |
   | **Repeatable Read**         | 같은 데이터를 여러 번 읽어도 항상 같은 결과를 보장. 팬텀 리드는 방지되지 않을 수 있음 |
   | **Serializable**            | 가장 높은 격리 수준. 모든 트랜잭션이 순차적으로 실행되는 것처럼 동작하지만 성능 저하 가능성 있음 |
6. **낙관적 / 비관적 동시성 제어**
   - **비관적(Pessimistic)**: 충돌이 자주 발생할 것으로 가정 → 미리 락 걸기
   - **낙관적(Optimistic)**: 충돌이 거의 없다고 가정 → 나중에 충돌 검사 후 재시도
<br/>
  
### 👀 핵심 요약
1. 동시성 문제는 여러 작업이 같은 자원을 동시에 사용할 때 발생한다.
2. 해결은 자원을 어떻게 "제한"하거나 "분산"해서 사용할지를 결정하는 것이다.
3. 적절한 도구(락, 메시지 큐, 트랜잭션 등)를 상황에 맞게 사용해야 한다.
