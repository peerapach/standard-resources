# Kubernetes Knowledge Base

## Pod Failure States

### CrashLoopBackOff
Container starts then crashes repeatedly. Causes:
- Application exits with non-zero code — check `kubectl logs <pod> --previous`
- OOMKilled: memory limit too low — increase `resources.limits.memory`
- Missing environment variable or secret
- Liveness probe failing too aggressively — increase `initialDelaySeconds`

### ImagePullBackOff / ErrImagePull
- Image does not exist at that tag
- Registry credentials missing — create imagePullSecret and reference in pod spec
- Registry is unreachable from the cluster network

### Pending (not scheduling)
- Insufficient CPU/memory on nodes — `kubectl describe pod` shows "Insufficient cpu"
- Node selector or affinity rules not matching any node
- PVC not bound — check `kubectl get pvc`
- Taint without toleration on target node

### OOMKilled
Container exceeded memory limit. Increase `resources.limits.memory` or fix memory leak.

### CreateContainerConfigError
Missing ConfigMap or Secret referenced in env/volume — ensure they exist in the same namespace.

## Service & Networking Issues

### Service not reachable
- `selector` in Service does not match pod labels — verify with `kubectl get endpoints`
- Port mismatch: Service `targetPort` must match container `containerPort`
- NetworkPolicy blocking traffic — check policies in the namespace

### Ingress returning 404
- `pathType` mismatch (Prefix vs Exact)
- Backend service name/port wrong
- Ingress class annotation missing or wrong

## Deployment Issues

### Rollout stuck
`kubectl rollout status deployment/<name>` hangs:
- New pods crashing (CrashLoopBackOff) — rollback with `kubectl rollout undo deployment/<name>`
- `maxUnavailable: 0` with no spare capacity for new pods

### Config changes not applying
- ConfigMap/Secret changes require pod restart — use `kubectl rollout restart deployment/<name>`
- Use `envFrom` with a hash in pod annotations to force rolling update on config change

## Resource Management
- Always set `resources.requests` and `resources.limits`
- HPA requires metrics-server installed; verify with `kubectl top nodes`
- PodDisruptionBudget: set to prevent all pods being removed during node drain

## Useful Diagnostic Commands
```bash
kubectl describe pod <pod>          # events and conditions
kubectl logs <pod> --previous       # last crash logs
kubectl get events --sort-by=.lastTimestamp
kubectl top pod                     # live resource usage
kubectl exec -it <pod> -- sh        # shell into running container
```
