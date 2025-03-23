# Argo Workflows

## Argo Workflows Core Concept 

### Workflow

Workflow 란 특정 목표/결과를 위해 실행되는 일련의 작업, 프로세스, 절차이다. Argo 와 DevOps tools 에서, workflow 는 특히 software application 의 배포/테스트/promotion 을 위ㅣ한 자동화된 일련의 절차를 의미한다.

Argo 에서, `Workflow` 는 k8s custom resource 를 의미한다. `Workflow` 는 user 가 복잡한 프로세스, 의존성, 조건을 선언적 방식으로 describe 할 수 있도록 하는 높은 수준의 추상화이다. `Workflow` 는 workflow 상태를 관리한다.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: hello-world-
spec:
  entrypoint: whalesay
  templates:
    - name: whalesay
      container:
        image: docker/whalesay
        command: [cowsay]
        args: ["hello world"]
```

Workflow 의 spec 은 두 가지 중요한 파트가 있다.

- `entrypoint` : workflow 의 진입점이 되는 template 이름을 명시
- `templates` : 실행될 절차/작업. 6가지 종류가 있다.

#### Template Definitions
##### Container

- 가장 기본적인 템플릿 타입. 컨테이너를 실행하는 역할을 함.

```yaml
- name: whalesay
  container:
    image: docker/whalesay
    command: [cowsay]
    args: ["hello world"]
```

##### Resource

k8s 자원을 생성/수정/제거 하는 작업

```yaml
- name: k8s-owner-reference
  resource:
    action: create
    manifest: |
      apiVersion: v1
      kind: ConfigMap
      metadata:
        generateName: owned-eg-
      data:
        some: value
```

##### Script

```yaml
- name: gen-random-int
  script:
    image: python:alpine3.6
    command: [python]
    source: |
      import random
      i = random.randint(1, 100)
      print(i)
```

##### Suspend

```yaml
templates:
  - name: wait-for-approval
    suspend: {}
```

#### Template Invocators
#####  DAG

```yaml
- name: diamond
  dag:
    tasks:
    - name: A
      template: echo
    - name: B
      dependencies: [A]
      template: echo
    - name: C
      dependencies: [A]
      template: echo
    - name: D
      dependencies: [B, C]
      template: echo
```

##### Steps

```yaml
templates:
  - name: my-workflow
    steps:
      - - name: step1
          template: my-container
      - - name: step2
          template: my-script

  - name: my-container
    container:
      image: alpine
      command: [echo, "Step 1 executed!"]

  - name: my-script
    script:
      image: python:3.8
      command: [python]
      source: |
        print("Step 2 executed!")
```

### Outputs

아래 예제에서는 step1에서 생성한 출력을 step2에서 사용함.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: output-example-
spec:
  entrypoint: main
  templates:
    - name: main
      steps:
        - - name: step1
            template: generate-output
        - - name: step2
            template: use-output
            arguments:
              parameters:
                - name: message
                  value: "{{steps.step1.outputs.result}}"

    - name: generate-output
      script:
        image: python:3.8
        command: [python]
        source: |
          print("Hello, Argo!")

    - name: use-output
      script:
        image: python:3.8
        command: [python]
        source: |
          import sys
          print(f"Received message: {sys.argv[1]}")
        args: ["{{inputs.parameters.message}}"]
```

> Q: outputs: 이런식의 선언이 없어도 되?
> 
> A: ㅇㅇ 없어도 됨. Argo Workflows에서 script, container 템플릿은 기본적으로 stdout의 마지막 줄을 outputs.result로 자동 저장함.

### WorkflowTemplate

WorkflowTemplate 은 재사용 가능한 worflow template 을 정의하는 resource 이다. workflow logic, parameters, metadata 등을 캡슐화 할 수 있다.
