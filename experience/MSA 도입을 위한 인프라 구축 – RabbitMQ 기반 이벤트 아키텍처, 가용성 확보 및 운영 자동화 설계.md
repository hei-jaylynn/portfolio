# MSA 도입을 위한 인프라 구축 – RabbitMQ 기반 이벤트 아키텍처, 가용성 확보 및 운영 자동화 설계

## 오픈소스 Spring Cloud / NetflixOSS 적용 적합성 검토

### Situation & Task
모놀리식 아키텍처를 5개의 마이크로서비스로 분리하는 프로젝트에서, **마이크로서비스 간 통신을 위한 인프라 요소를 검토하고 구축하는 역할**을 담당했습니다.   
MSA 도입은 조직 내 새로운 시도였으며, **NetflixOSS를 활용하기로 결정했지만, 시스템 요구사항을 충족할 수 있을지 검증이 필요했습니다.**

### Action

- NetflixOSS 기술 블로그와 Spring Cloud 공식 문서를 분석
- API Gateway, Service Discovery, Load Balancer, Circuit Breaker 등 주요 컴포넌트의 역할과 기능을 정리
- 샘플 프로젝트를 개발해 각 모듈의 동작을 검증
- 다양한 Spring 설정을 A/B 테스트하여 최적화된 설정을 적용

### Result
- 마이크로서비스 아키텍처로 전환 후 서비스 오픈 완료
- MSA에 관심 있는 **임직원을 대상으로 Spring Cloud 기반의 내부 교육을 기획하여 이론/실습 강의를 2회 진행**  
- **‘[누구나 쉽게 Zuul, Eureka, Hystrix](https://docs.google.com/document/d/1ANXSKh6LM0Cm0WKwj6y7EHg42JGtEE-Ea17Q966577U/edit?usp=sharing)’ 핸드북을 제작해 사내 게시판에 공유** 및 세미나에서 프로젝트 경험 공유

---

## RabbitMQ 기반 이벤트 아키텍처 구축 및 가용성 확보

### Task
- 마이크로서비스 결합도를 낮추면서 서비스 변경사항을 다른 서비스로 전달하여 적절한 처리를 실행할 수 있게 이벤트 흐름 설계

### Action

**AS-IS 시스템 분석 및 이벤트 특성에 적합한 RabbitMQ 채택** 
- 각 메시지가 특정 대상에게만 전달되며 트래픽이 예측 가능
- 메시지가 소비 후 삭제되는 일회성 이벤트
- 즉시 후속 처리가 필요없는 이벤트  

**RabbitMQ 단일 장애 지점(Single Point of Failure) 제거**
- **이중화 구성 및 미러링 정책 적용** → 노드 장애 시에도 메시지 손실을 방지
- **HAProxy를 Load Balancer로 구성** → 부하분산 및 클라이언트와 서버 간 연결 안정성 보장

**서비스팀의 비효율적인 메시징 패턴 개선**
- Publisher가 동일한 메시지를 여러 Subscriber에게 직접 전송하는 **비효율적인 Direct 방식**을 사용하고 있었음
- **Fanout 방식의 RabbitMQ Exchange로 변경**하여 중복 발행 제거 및 메시지 처리 성능 최적화
- 개선된 구조가 기존 비즈니스 요구사항을 충족하는지 테스트 케이스 작성 및 검증

### Result
- **RabbitMQ 장애 발생 시에도 Failover가 원활하게 이루어지는 환경**을 구축
- **[RabbitMQ 활용 가이드](https://www.slideshare.net/YoonjeongChloeKwon/ss-74139329#1)를 작성**하여 운영팀이 장애 대응 및 설정 변경을 보다 원활하게 수행할 수 있도록 지원
- 본 가이드는 다른 팀에서도 RabbitMQ 도입 시 참고 문서로 활용되며, **기술적 노하우를 확산하는 기회**로 이어짐


---

## 수작업 없이 빠르고 안정적으로 – 설정 및 배포 자동화 구축

### Problem
기존에는 **서비스 설정 변경 시 약 20개의 EC2 인스턴스에서 개별적으로 수작업을 수행**해야 했으며,    
테스트, 스테이징, 운영 환경을 포함하면 최소 60번의 설정 적용이 필요했습니다.   

### Solution
- **spring-cloud-bus 모듈을 도입하여 설정 변경을 한 번의 요청으로 전체 인스턴스에 자동 반영**하도록 개선
- GoCD 기반의 CI/CD 자동화 환경을 구축해 빌드 및 배포를 자동화
- SonarQube 및 Fortify를 연계하여 코드 품질 및 보안 검증을 개발 단계에서 자동화

### Impact
**애플리케이션 설정 적용 시간이 90% 단축**되었으며, **설정 변경과 배포가 일관되게 적용**할 수 있는 환경을 만들어 운영 안정성을 확보했습니다.

