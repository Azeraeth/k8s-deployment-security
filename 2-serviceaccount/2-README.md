# Standard Kuberentes Deployment with ServiceAccount

## Why specify a service account

https://kubernetes.io/docs/concepts/security/service-accounts/
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/


## Standard Deployment with Service Account

Apply this standard Kubernetes Deployment using the `nginx:mainline` image running as the `k8s-security` service account in the `k8s-security` namespace


### Deploy

This creates a namespace, a service account and 1 pod deployment running the `nginx:mainline` image as the `k8s-security` service account.

```shell
kubectl apply -f 2-k8s-deploy.yaml
namespace/k8s-security created
serviceaccount/k8s-security created
deployment.apps/k8s-serviceaccount created
```


### View ServiceAccount in pod

View the service account name in the pod

```shell
kubectl get pod <pod-name> -ojsonpath="{.spec.serviceAccountName}"
k8s-security
```

Alternatively, you could describe the pod or output to yaml and look for `serviceAccountName`

## Cleanup

Once done, you can remove the objects

```shell
kubectl delete -f 2-k8s-deploy.yaml
namespace "k8s-security" deleted
serviceaccount "k8s-security" deleted
deployment.apps "k8s-serviceaccount" deleted
```