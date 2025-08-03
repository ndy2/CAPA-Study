ok argo rollout 은 어떻게 setWeight 기반 트래픽 분배를 퍼센트로 지원해?
kubernetes 는 기본적으로 파드 개수 단위로만 분배할 수 있지 않아?

예를들어 총 replicas 가 3 으로 설정된 rollout 인

```
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: my-rollout
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx:1.25
          ports:
            - containerPort: 80
  strategy:
    canary:
      steps:
        - setWeight: 20
        - pause: {duration: 20s}
        - setWeight: 100
```

이런 rollout 에 어떻게 20%의 요청만 분배 할 수 있어?

---

아주 정확한 질문임 👍
맞음, Kubernetes 자체만으로는 트래픽을 Pod 단위로만 분배할 수 있고, 트래픽의 퍼센트 조절(setWeight) 같은 건 기본 기능으론 불가능함.

그럼에도 Argo Rollouts가 setWeight: 20 처럼 퍼센트 기반 트래픽 분배를 지원할 수 있는 이유는:

Ingress Controller나 Service Mesh와 연동해서,
트래픽 라우팅을 외부 컴포넌트가 처리하도록 설정하기 때문임.

⸻

🎯 결론 먼저

🔥 Argo Rollouts에서 setWeight가 진짜 퍼센트 트래픽 분배를 하려면:
	•	strategy.canary.trafficRouting 필드를 설정해야 함
	•	그리고 Istio, NGINX Ingress, SMI, AWS ALB 등의 라우팅 지원 컴포넌트가 필요함

그렇지 않으면 setWeight는 단순히 **설명적(의미만 있음)**일 뿐,
트래픽 분배는 실제로 되지 않음 (= 사용자가 수동으로 라우팅하거나 관찰만 가능)

⸻

⚙️ 예시: NGINX Ingress Controller 연동

strategy:
  canary:
    steps:
      - setWeight: 20
      - pause: {duration: 30s}
      - setWeight: 100
    trafficRouting:
      nginx:
        stableIngress: my-app-ingress

그리고 Ingress 설정은 다음처럼 되어 있어야 함:

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "20"
spec:
  rules:
    - host: my-app.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-app-stable
                port:
                  number: 80

Argo Rollouts가 이 Ingress 리소스를 자동으로 조작하면서
canary-weight 값을 20 → 100 으로 바꿔줌.

⸻

💡 만약 trafficRouting 설정이 없다면?

Argo Rollouts는 다음만 수행함:
	•	새 ReplicaSet 생성
	•	replicas, maxSurge, maxUnavailable 에 따라 파드 개수 조절
	•	하지만 트래픽은 여전히 K8s 서비스가 알아서 균등 분배

즉, 이 경우엔:

setWeight: 20이 의미하는 “트래픽 20%”는 단순히 Canary 파드 수를 전체 중 일부로 만든다는 의미일 뿐,
실제로 클라이언트 요청이 20%만 새 파드로 가는 건 보장되지 않음.

⸻

📌 요약

상황	트래픽 분배 실제 작동 여부
trafficRouting 없음	❌ 단순히 파드 개수만 조절함. 트래픽은 불확실
trafficRouting + NGINX / Istio 등 있음	✅ 실제로 요청 비율이 제어됨
Kubernetes 기본만 사용	❌ 트래픽 비율 조절은 안 됨. 수동/관찰 용도로만 사용


⸻

원하면
	•	Istio 기반 예제
	•	NGINX 기반 예제
	•	ALB 기반 Canary 설정

이 중 하나 골라서 다음 단계로 넘어갈 수 있음.
너가 쓰는 Ingress Controller가 뭔지도 알려주면 좋고.