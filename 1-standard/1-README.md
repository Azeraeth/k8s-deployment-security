# Standard Kuberentes Deployment

## Standard Deployment

Apply this standard Kubernetes Deployment using the `nginx:mainline` image running as the `default` service account in the `k8s-security` namespace


### Deploy

This creates a namespace and 1 pod deployment running the `nginx:mainline` image. Nothing fancy happening here

```shell
kubectl apply -f 1-k8s-deploy.yaml
namespace/k8s-security created
deployment.apps/k8s-standard created
```


### View ServiceAccount in pod

View the service account name in the pod

```shell
kubectl get pod <pod-name> -ojsonpath="{.spec.serviceAccountName}"
default
```

## Cleanup

Once done, you can remove the objects

```shell
kubectl delete -f 1-k8s-deploy.yaml
namespace "k8s-security" deleted
deployment.apps "k8s-standard" deleted
```