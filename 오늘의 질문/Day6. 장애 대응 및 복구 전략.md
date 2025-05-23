# 장애 대응 및 복구 전략
### 시스템 장애 발생 시 신속하게 대응하고 복구하기 위해 어떤 전략을 수립하고 적용했는지 구체적으로 설명해 주세요.
힌트: 장애 탐지, 알림 시스템, 롤백 전략, 재시도 메커니즘 등의 구체적인 대응 방식을 중심으로 설명해 주세요.
<br/> <br/>

### 🎯 CI/CD 연동 알림 시스템을 활용한 장애 대응 체계 구축 경험
시스템 장애 발생 시 신속한 대응을 위해 CI/CD 파이프라인과 알림 시스템을 활용한 전략을 적용한 경험이 있습니다. 
직접 구현한 것은 아니지만, GitHub에 Pull Request를 생성하면 자동으로 CI/CD 파이프라인이 실행되고, 빌드나 테스트가 실패할 경우 Discord를 통해 관련 알림이 실시간으로 전달되는 시스템을 이용한 적이 있습니다. 
이를 통해 장애나 오류 발생 시 빠르게 인지하고, 팀원들과 즉시 공유하여 신속하게 조치할 수 있는 대응 체계를 갖추었습니다.
<br/> <br/> 

---

## 🌱 장애 대응 및 복구 전략
장애 대응 및 복구 전략은 시스템에 장애(에러, 다운타임, 성능 저하 등)가 발생했을 때 빠르게 원인을 파악하고 서비스 정상 상태로 복구하기 위한 일련의 준비된 계획과 기술적 접근을 의미한다.
<br/>

### 🥕 필요성
- 서비스 연속성 보장 : 고객이 사용하는 서비스가 예기치 않게 중단되면 신뢰도와 비즈니스에 큰 영향을 줄 수 있다.
- 장애 확산 방지 : 초기 장애를 조기에 탐지하고 대응하면, 전체 시스템으로 확산되는 것을 방지할 수 있다.
- 빠른 복구 시간 MTTR(Mean Time To Recovery) 단축 : 전략이 준비되어 있으면 장애 발생 후 복구까지의 시간을 최소화할 수 있다.
- 재발 방지와 품질 개선 : 장애 원인을 분석하고 대응 프로세스를 개선하면, 비슷한 문제가 다시 발생하는 것을 방지할 수 있다.
<br/>

### 🧑🏻‍🌾 장애 대응 및 복구 전략
1. **장애 탐지 Monitoring & Observability**
    1. 애플리케이션 및 인프라 모니터링 도입
        - Prometheus, Grafana, Datadog, New Relic 등을 활용해 시스템 상태 실시간 관찰
        - CPU, Memory , 네트워크, DB 성능 등 핵심 지표 수집
    2. 로그 기반 이상 징후 탐지
        - ELK(Elasticsearch, Logstash, Kibana) 스택 또는 Loki + Grafana로 로그 분석
        - 에러 로그나 비정상적인 응답 패턴 자동 감지
    3. 헬스 체크 및 심플 테스트
        - 서비스 상태를 점검하는 주기적인 health check endpoint 구성
        - 배포 후 smoke test 또는 sanity test 실행
    
2. **알림 시스템 Alerting**
    1. 자동화된 알림 연동
        - 장애 발생 시 Slack, Discord, 이메일, SMS 등으로 실시간 알림 전송
        - 예: GitHub PR 실패 시 CI/CD 파이프라인에서 Discord로 알림 전송
    2. 임계값 기반 알림
        - 메모리 사용률 80% 초과, 오류율 증가 등 사전에 정의된 조건 발생 시 자동 경보
    3. 알림 우선순위 설정
        - P1(즉시 대응), P2(1시간 내 대응) 등으로 알림을 분류하여 효율적인 대응 유도
    
3. **롤백 전략 Rollback Strategy**
    1. Blue-Green Deployment
        - 새 버전을 별도 환경에서 배포하고 정상 확인 후 트래픽 전환
        - 장애 발생 시 즉시 이전 버전으로 롤백 가능
    2. Canary Release
        - 트래픽 일부만 새로운 버전으로 전환하여 안정성 검증
        - 문제 발생 시 자동 롤백 시스템 구성 (예: Argo Rollouts)
    3. 기능 플래그 Featrue Toggle
        - 문제가 되는 기능만 비활성화하여 전체 서비스에는 영향 최소화
    
4. **재시도 및 복구 매커니즘 Retry & Recovery**
    1. 재시도 로직 적용
        - 일시적인 오류(DB 커넥션, 네트워크 등)에 대해 제한된 횟수로 재시도
        - 지수 백오프(Exponential Backoff) 전략 적용
    2. Circuit Breaker 패턴
    3. Failover 및 이중화 구성
        - DB, 캐시 서버, 애플리케이션 서버 등을 다중화하여 하나의 장애가 전체 서비스에 영향을 미치지 않도록 구성
  
5. **대응 프로세스 수립 및 사후 분석**
    1. Incidnet Response Runbook 작성
        - 장애 발생 시 어떤 절차로 대응할지 문서화
    2. Postmortem 회고
        - 장애 원인 분석 및 재발 방지를 위한 회고 작성
        - 대응 시간, 커뮤니케이션 문제, 알림 정확도 등을 점검
<br/>

### 👀 핵심 요약
- 장애 대응 및 복구 전략은 시스템 장애 발생 시, 빠르게 인지하고 원인을 파악해 서비스 정상화를 위한 절차와 기술을 총망라한 대응 체계이다.
- 실시간 모니터링 및 알림 시스템을 통해 장애를 빠르게 탐지하고, 우선순위에 따라 신속히 대응할 수 있다.
- **롤백 전략(Blue-Green, Canary, 기능 플래그)**을 통해 안정적인 배포 및 장애 시 빠른 복구가 가능하다.
- 재시도 로직, Circuit Breaker, 이중화 구성 등을 통해 시스템 회복력과 안정성을 강화한다.
- 장애 후에는 Runbook 및 Postmortem 회고를 통해 원인을 분석하고, 재발을 방지하는 개선 프로세스를 수행한다.
