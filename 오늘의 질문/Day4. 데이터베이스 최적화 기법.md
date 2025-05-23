# 데이터베이스 최적화 기법

### DB 인덱스, 샤딩, 복제 등의 기법을 활용해 성능을 개선한 경험이 있다면, 문제 상황과 적용 방식, 결과를 함께 설명해 주세요.
힌트: 쿼리 성능 문제를 어떤 방식으로 진단했고, 그에 따라 어떤 인덱스를 어떻게 설계했는지가 중요합니다. 단순히 "인덱스를 추가했다"보다는, 실행 계획(EXPLAIN) 분석이나 파티셔닝·샤딩을 통한 구조적 접근이 담기면 훨씬 설득력 있는 답변이 됩니다.
<br/> <br/>

### 🎯 데이터베이스 최적화 경험
위치 기반 사용자 매칭 시스템을 설계하면서, 초기 사용자 수가 많지 않아도 데이터 증가에 따라 필연적으로 성능 저하가 발생할 수 있는 부분들을 사전에 예측하고, 이에 대응하기 위한 최적화 기법들을 구조에 반영했습니다.
<br/> <br/> 

### 🪧 적용한 성능 최적화 기법

1. **Redis GEO + Hash 캐시로 DB 조회 최소화**
   - **문제 상황**: 위치 기반 탐색에서 수많은 거리 계산과 사용자 프로필 정보 조합을 위해 MySQL을 반복적으로 조회하게 되어 성능 병목이 발생할 수 있었습니다.
   - **적용 방식**:
     - **Redis GEO** 기능을 활용하여 반경 내 사용자 위치를 빠르게 탐색했습니다.
     - 사용자 관심사, 역할, 경력 정보는 **Redis Hash**에 캐시하여 DB 접근 없이 조회 가능하도록 설계했습니다.
   - **성과**:
     - 대부분의 사용자 정보 조회가 캐시에서 처리되어 DB 부하를 최소화할 수 있었습니다.
     - 초당 수천 건의 요청에도 서버 CPU 사용률이 안정적으로 유지되었습니다.

2. **DB 인덱스 설계**
   - **문제 상황**: `client` 테이블에서 `role`, `career`, `id` 기준으로 필터링 및 정렬이 자주 발생할 수 있습니다.
   - **적용 방식**:
     - 예상 쿼리 경로에 따라 **복합 인덱스** (`role`, `career`, `client_id` 등)을 사전 정의하여 인덱스를 설계했습니다.
   - **성과**:
     - 실제 운영 중에도 쿼리 성능 저하 없이 안정적인 탐색 속도를 유지할 수 있었습니다.

3. **샤딩 설계 고려**
   - **문제 상황**: 사용자 수가 증가함에 따라 단일 테이블 구조로는 I/O 병목이 발생할 수 있었습니다.
   - **적용 방식**:
     - 설계 단계에서부터 `client`, `it_topic` 등의 테이블을 사용자 ID 범위 기반으로 **샤딩** 가능하도록 파티셔닝 기준을 정의하고, Redis 캐시 키도 `client:{userId}` 형태로 샤딩 구조 확장을 고려하여 네임스페이스화 했습니다.
   - **성과**:
     - 실제 샤딩이 필요해질 때 무중단 확장을 염두에 둔 구조로 설계했습니다

### 배운점
데이터베이스 최적화가 단순한 성능 향상이 아니라, 시스템의 생사와 직결되는 핵심이라는 사실을 절실히 깨달았습니다. Redis 캐시와 DB 인덱스를 활용하는 것만으로 성능 병목을 해소하고, 몇 배 더 빠른 응답 성능을 구현할 수 있었습니다. 하지만 그보다 더 충격적이었던 것은, 샤딩 설계와 같은 구조적 접근이 시스템의 확장성을 좌우한다는 점이었습니다.
<br/> 

---

## 🌱 데이터베이스 최적화 기법
데이터베이스 성능은 단순한 쿼리 속도 이상의 문제로, 트래픽이 증가하거나 데이터가 기하급수적으로 쌓이는 상황에서도 시스템이 일관된 응답할 수 있도록 설계하는 것이 진짜 최적화이다.
<br/> <br/> 

### 🧑🏻‍🌾 데이터베이스 최적화 기법
1. **인덱스 최적화 Index Optimization** : 자주 사용하는 쿼리의 WHERE, ORDER BY, JOIN 조건에 맞춰 적절한 단일/복합 인덱스를 설계하는 것이 핵심이다.
   - EXPLAIN 명령어로 실행 계획을 분석해 Full Table Scan 여부를 확인한다.
   - 자주 조회되는 필드를 앞에 두고, 선택도가 높은 컬럼 순서로 복합 인덱스를 구성한다.
   - 불필요한 인덱스는 오히려 쓰기 성능을 떨어뜨리므로 주기적인 인덱스 점검도 중요하다.

2. **쿼리 리팩토링 Query Refactoring** : 불필요한 SELECT *, 중복된 서브쿼리, 비효율적인 JOIN을 제거하고, 최소한의 연산으로 최대의 결과를 얻도록 설계한다.
   - 조인보다 버스쿼리 분리가 나은 경우도 있으니 상황에 따라 유연하게 대응한다.
   - 조건에 따라 UNION ALL이 UNION보다 빠를 수 있다.
   - 페이지네이션에서 OFFSET이 클 경우 커서 기반 페이징으로 전환하는 것도 성능에 큰 영향을 준다.
  
3. **파티셔닝 및 샤딩 Partitioning & Sharding** : 테이블이 너무 커지면 물리적으로 나누어 조회 범위를 줄이는 것이 핵심이다.
   - MySQL에서는 날짜, 범위 기반 PARTITION BY RANGE 또는 LIST로 파티셔닝한다.
   - 샤딩 시에는 사용자 ID, 지역 등 균등하게 분산 가능한 키를 기준으로 한다.
   - 샤딩 구조에서는 중앙 관리 메타 테이블과 일관된 키 네이밍 전략이 필수이다.
  
4. **캐싱 전략 Cashing Strategy** : 변하지 않는 데이터를 계속 DB에서 가져오는 것은 낭비이다. 읽기 빈도가 높은 데이터는 캐시로 우선 조회하고, 만료 정책을 설정해 일관성을 유지한다.
   - 사용자 정보, 검색 결과, 공통 코드 등은 Redis 또는 메모리 기반 LRU 캐시로 처리한다.
   - 캐시 미스 시 데이터 조회 후 저장하는 lazy-loading 방식으로 일반적이다.
   - 캐시 키는 prefix:{identifier}형태로 구성해 네임스페이스와 충돌 방지를 고려한다.
<br/>

### 👀 핵심 요약
1. 데이터베이스 최적화는 단순히 속도를 빠르게 만드는 것이 아니라, **시스템 전체의 안정성과 확장성 확보를 위한 설계 전략**이다.
2. 캐싱, 인덱스, 쿼리 최적화, 샤딩은 각기 다른 목적(읽기 성능, 구조 확장 등)을 가지며 **조합해서 사용할 때** 최대의 효과를 발휘한다.
3. 최적화의 핵심은 **사전 예측과 구조적 대응**이며, 트래픽 폭주 상황에서도 흔들리지 않는 설계를 해야 한다.
