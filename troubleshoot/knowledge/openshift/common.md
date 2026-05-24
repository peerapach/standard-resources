# OpenShift Knowledge Base

## OpenShift vs Kubernetes Differences
- Pods run as random non-root UID by default (SCC: restricted) — images must not require root
- `oc` CLI wraps `kubectl` with OpenShift-specific resources (Route, BuildConfig, ImageStream)
- Projects = Kubernetes Namespaces with additional RBAC and quotas

## Security Context Constraints (SCC)

### "unable to validate against any security context constraint"
Container requests capabilities or root access that no SCC allows:
- Add `anyuid` SCC if image requires a fixed UID: `oc adm policy add-scc-to-serviceaccount anyuid -z <sa> -n <ns>`
- Prefer fixing the image to run as non-root instead of granting anyuid

### "permission denied" in container filesystem
Volume mount ownership issue — set `fsGroup` in pod securityContext, or use `supplementalGroups`

## Routes & Ingress
- OpenShift Route is the native ingress; HAProxy terminates TLS at the router
- `oc expose svc/<name>` creates a Route automatically
- TLS passthrough: set `termination: passthrough` in Route spec
- Custom hostname: set `spec.host` in Route; DNS must point to the router

## ImageStreams & Builds

### ImageStream tag not updating
- Scheduled import disabled — enable with `oc tag --scheduled=true`
- External registry credentials missing — link pull secret to the service account

### BuildConfig fails
- `oc logs bc/<name>` for build logs
- Source strategy: ensure Dockerfile or S2I builder image is accessible
- Resource quota exceeded — check `oc describe quota` in the namespace

## DeploymentConfig vs Deployment
- `DeploymentConfig` is OpenShift-native with image change triggers; being deprecated in favour of standard `Deployment`
- Migrate: `oc convert` or rewrite manifests using standard `Deployment`

## Quota & LimitRange
- ResourceQuota exceeded: `oc describe quota -n <ns>` — reduce requests or ask admin to raise quota
- LimitRange sets default requests/limits — check with `oc describe limitrange`

## Common oc Commands
```bash
oc status                          # project overview
oc get events --sort-by=.lastTimestamp
oc describe pod <pod>
oc logs <pod> --previous
oc rsh <pod>                       # remote shell
oc adm top pods                    # resource usage
oc rollout status dc/<name>
oc rollout undo dc/<name>
```

## Networking
- OpenShift SDN / OVN-Kubernetes: NetworkPolicy is supported
- Egress IP: assign a stable outbound IP to a namespace — requires cluster-admin
- Service Mesh (Istio/Maistra): additional sidecar injection required; check `oc get pods` for istio-proxy container
