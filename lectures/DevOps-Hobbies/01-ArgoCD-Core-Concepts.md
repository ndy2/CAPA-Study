# 01 ArgoCD Core Concepts

- Declarative
- Versioned and Immutable
- Pulled Automatically
- Continuously Reconciled

ArgoCD Core Components

- API Server
  - UI/CLI 로 부터 gRPC/REST 요청을 받아 처리 한다.
- Repository Server
  - 저장소 
- Application Controller
  - Live State 와 Target State 를 비교하여 실제 Sync 를 수행
