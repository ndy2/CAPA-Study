다섯가지 원칙
- Declarative configuration
    - 전통적인 command 와 action 을 통한 구성 대신 GitOps 는 desired state 를 기술(declare) 하는 방식으로 동작
- Immutable storage
    - GitOps 의 관점에서 Git 은 단순히 VCS solution 으로 동작하지 않음
    - Git 은 Immutable Storage 로써 동작
    - 또한 정적인 reference point 로 동작하고, history 와 traceability 를 제공하고 종종 sources of truth 로 동작함
- Automation
    - 자동화는 GitOps 의 핵심 요소!
    - commit 이 이루어진 이후의 수동 작업을 제거하는 것이 중요!
- Software agents
    - 선언된 설정(configuration)을 실제 환경에 반영하는 역할
- Closed loop
    - 실제 상태 (actual state) 와 원하는 상태 (desired state) 를 계속 비교하며 지속적으로 피드백을 제공

으로 `LFS256` 강의와 `예제로 배우는 ArgoCD` 책에서는 소개하지만 

https://opengitops.dev/ 에 따르면 `Oct 9, 2021` 에 발표된 `v1.0.0` 에서는 principles 가 다음의 4가지로 정리된 것으로 보인다.

![img.png](images/gitops-principles.png)
