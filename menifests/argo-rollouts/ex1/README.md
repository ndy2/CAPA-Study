```bash
k create -f my-rollout.rollout.yaml
k argo rollouts get rollout my-rollout

kubectl argo rollouts set image my-rollout my-app=nginx:1.26
kubectl argo rollouts promote my-rollout
kubectl argo rollouts undo my-rollout
```