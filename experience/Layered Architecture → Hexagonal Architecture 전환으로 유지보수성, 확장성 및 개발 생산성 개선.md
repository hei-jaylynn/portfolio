# Layered Architecture → Hexagonal Architecture 전환으로 유지보수성, 확장성 및 개발 생산성 개선

### Situation
Layered Architecture 기반의 시스템을 운영하는 개발팀에 새로 참여하게 되었습니다.   
코드 복잡도 문제를 호소하였고, 클래스 순환 참조를 이미 몇 번 겪은 상황이었습니다.  

코드를 분석해보니, 
- DB 모델을 도메인 모델로 직접 사용
- 서비스 클래스에서 Repository를 직접 호출하고 DB 모델을 조작하여 비즈니스 로직을 수행
하고 있었습니다.

이런 이유로
- DB 의존성이 서비스 계층에 강하게 결합되어 코드 변경이 어렵고
- 서비스 클래스가 비대해져 재사용이 어려웠으며
- 한 클래스에서 소유하고 있는 타 서비스 의존성이 많아 순환참조가 발생하기 쉬운 구조였습니다.

### Task
이를 해결하기 위해 **Hexagonal Architecture(Ports and Adapters Pattern) 적용을 제안**하고 다음과 같은 목표를 세웠습니다.

- 서비스를 작은 단위로 분리하여 코드 중복 및 순환 참조 문제 해결
- 인터페이스를 중심으로 DB와 통신하여 서비스 계층으로부터 DB 모델 의존성 제거

### Action
거대한 코드 베이스를 한 번에 변경하기 어려웠기 때문에 점진적으로 전환 전략을 수립하여 적용했습니다.

- 도메인 중심 구조로 점진적 개발
	- 새로 추가하는 기능을 **유스케이스 단위로 서비스 클래스 작성**
 	- DB 등 인프라스트럭처와 통신은 **In/Out Port 인터페이스를 활용하여 의존성 관리**
	- **의존성 방향을 단방향으로 설정**하여 서비스 간 결합도를 낮추도록 설계
- 2차 개선 계획 수립
	- 바운디드 컨텍스트(Bounded Context) 재조정
	- 각 도메인을 독립적인 모듈로 분리하여 빌드 아티팩트로 관리
	- 애플리케이션에서 불필요한 의존성을 제거
	- 애플리케이션 빌드 시간 축소

### Result

헥사고날 아키텍처 도입 후 동료 개발자들로부터 **도메인 중심의 코드 작성이 가능해졌고**, **단위 테스트 작성이 쉬워졌다는 피드백**을 받았습니다.   
또한 서비스 결합도가 낮아지면서 **유지보수 및 확장성 향상** 되었고 **각 개발자가 코드 충돌없이 병렬 작업이 가능해져 개발 생산성이 높아졌습니다.**   
물론 코드의 순환 참조 문제를 예방하는 효과도 얻을 수 있었습니다.    
다만, 헥사고날 아키텍처 적용 전, 후의 코드 복잡도나 클래스 또는 패키지 간 순환 참조 등 코드 품질 수치를 기록해 두지 못한 점은 아쉬움으로 남습니다.
