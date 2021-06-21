# Deployment ARGOCD

```
kubectl config get-contexts
export DOMOKAI_DEV=<replace_config_context>
```

# helm config template
```
helm repo add argo https://argoproj.github.io/argo-helm
helm template argo/argo-cd -n argocd > argocd_template_helm.yaml
```
- Please add `metadata/namespace:argocd` on each manifest resource
- Split the helm template out in different yaml
- Remove `helm.sh/chart: argo-cd-3.6.8` , `app.kubernetes.io/managed-by: Helm` labels

# helm template / local k3d cluster
```
k3d cluster create platform-domokai-k3d --agents 1
kubectl create namespace argocd
helm install argo/argo-cd --generate-name -n argocd
kubectl -n argocd get pods --watch
kubectl -n argocd get svc
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
kubectl port-forward svc/argo-cd-1624308726-argocd-server -n argocd 8080:443
```
# Running using kustomize
- Go to domokai-kubernetes/domokai-dev folder
```
kustomize build argocd | kubectl --context=$DOMOKAI_DEV apply -f - -R --record
```

kubectl -n argocd get all
kubectl -n argocd get pods --watch