---
url: https://trainingportal.linuxfoundation.org/learn/course/devops-and-workflow-management-with-argo-lfs256
---

## Introduction

### What is GitOps

GitOps 란 현대적인 소프트웨어 배포 방법의 기본이되는 원칙을 포함합니다.

[GITOPS-PRINCIPLES](../../notes/GITOPS-PRINCIPLES.md)

### What is Argo

- a set of k8s-native tools that enhance the workflow management capabilities of k8s

Argo 는 GitOps 의 강건함을 k8s cluster 에 적용합니다.

1. Argo CD – GitOps 방식의 Kubernetes 애플리케이션 배포 및 관리 도구
2. Argo Workflows – Kubernetes 에서 복잡한 CI/CD 및 데이터 처리 워크플로우를 실행하는 엔진
3. Argo Rollouts – Kubernetes 에서 카나리아, 블루-그린, 재시도 등 배포 전략을 지원하는 컨트롤러
4. Argo Events – Argo Workflows 및 다른 Argo 구성요소에서 사용할 수 있는 이벤트 기반 트리거 시스템
5. Argo CLI - 위 요소에 대한 CLI 도구

