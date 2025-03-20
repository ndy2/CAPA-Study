---
url: https://trainingportal.linuxfoundation.org/learn/course/devops-and-workflow-management-with-argo-lfs256
---

## Inroduction

### What is GitOps

GitOps 란 현대적인 소프트웨어 배포 방법의 기본이되는 원칙을 포함합니다.

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

### What is Argo

- a set of k8s-native tools that enhance the workflow management capabilities of k8s

Argo 는 GitOps 의 강건함을 k8s cluster 에 적용합니다.

1. Argo CD – GitOps 방식의 Kubernetes 애플리케이션 배포 및 관리 도구
2. Argo Workflows – Kubernetes 에서 복잡한 CI/CD 및 데이터 처리 워크플로우를 실행하는 엔진
3. Argo Rollouts – Kubernetes 에서 카나리아, 블루-그린, 재시도 등 배포 전략을 지원하는 컨트롤러
4. Argo Events – Argo Workflows 및 다른 Argo 구성요소에서 사용할 수 있는 이벤트 기반 트리거 시스템
5. Argo CLI - 위 요소에 대한 CLI 도구

