좋아요! 지금까지 배운 내용을 바탕으로 실습 퀴즈를 준비해봤습니다. 각 문제는 실습을 통해 직접 해결할 수 있도록 구성했어요.

---

### **실습 퀴즈**

#### 1. **TTL 설정**
- 워크플로우가 완료된 후 30분 뒤에 자동으로 삭제되도록 설정하세요.
- 워크플로우는 `whalesay` 컨테이너를 실행하며, "Hello, TTL!" 메시지를 출력해야 합니다.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: hello-world-
spec:
  ttlStrategy:
    secondsAfterCompletion: 1800
  entrypoint: whalesay
  templates:
  - name: whalesay
    container:
      image: docker/whalesay:latest
      command: [cowsay]
      args: ["Hello, TTL!"]
```

---

#### 2. **DAG Template**
- 다음 조건을 만족하는 DAG 템플릿을 작성하세요:
  1. `task-a`가 먼저 실행됩니다.
  2. `task-b`와 `task-c`는 `task-a`가 완료된 후 병렬로 실행됩니다.
  3. 각 작업은 `whalesay` 컨테이너를 사용하며, 작업 이름을 출력해야 합니다.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: dag-template-example-
spec:
  entrypoint: dag-example
  templates:
  - name: dag-example
    dag:
      tasks:
      - name: task-a
        template: whalesay
        arguments:
          parameters:
          - name: task-name
            value: task-a
      - name: task-b
        template: whalesay
        dependencies: [task-a]
        arguments:
          parameters:
          - name: task-name
            value: task-b
      - name: task-c
        template: whalesay
        dependencies: [task-a]
        arguments:
          parameters:
          - name: task-name
            value: task-c
  - name: whalesay
    inputs:
      parameters:
      - name: task-name
    container:
      image: docker/whalesay:latest
      command: [cowsay]
      args: ["Hello from {{inputs.parameters.task-name}}!"]
```

---

#### 3. **Suspend Template**
- 워크플로우의 두 번째 단계에서 Suspend 상태로 중지되도록 설정하세요.
- Suspend 상태에서 1분 후 자동으로 재개되도록 설정하세요.
- 각 단계는 `whalesay` 컨테이너를 사용하며, 단계 이름을 출력해야 합니다.

---

#### 4. **Step-Level Timeout**
- 워크플로우의 각 단계에 10초의 타임아웃을 설정하세요.
- 타임아웃이 초과되면 해당 단계가 실패하도록 설정하세요.
- 각 단계는 `whalesay` 컨테이너를 사용하며, "Step Timeout Test" 메시지를 출력해야 합니다.

---

#### 5. **Workflow Status**
- 워크플로우를 실행한 후, 다음 상태를 확인하세요:
  1. `Pending`
  2. `Running`
  3. `Succeeded`
- 각 상태를 CLI 명령어를 사용해 확인하고, 결과를 기록하세요.

---

### **진행 방법**
1. 각 문제를 해결하기 위한 YAML 파일을 작성하세요.
2. 작성한 YAML 파일을 실행하고 결과를 확인하세요.
3. 문제가 있거나 궁금한 점이 있으면 언제든 물어보세요!

---

어떤 문제부터 시작해볼까요? 😊