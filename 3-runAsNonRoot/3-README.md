# Standard Kuberentes Deployment with runAsNonRoot

## Why specify runAsNonRoot

This will completely prevent the pod from running as the root user, more of a "sledgehammer" approach.

Here is an excellent page that describes why and why not: [Server As Code](https://serverascode.com/2023/09/02/runasnonroot-vs-runasuser.html#differences-between-runasnonroot-and-runasuser)

`runAsUser` is more fine-grained and can be used togther with `runAsNonRoot`

## Standard Deployment with runAsNonRoot


### Deploy

This creates a namespace, a service account and 1 pod deployment running the `nginx:mainline` image with `runAsNonRoot` set

```shell
$ kubectl apply -f 3-k8s-deploy.yaml
namespace/k8s-security created
serviceaccount/k8s-security created
deployment.apps/k8s-serviceaccount created
```

### Check pod status

Check the status of the pod:

```shell
$ kubectl -n k8s-security get pods
NAME                                 READY   STATUS                       RESTARTS   AGE
k8s-serviceaccount-9bc8c5887-h86jq   0/1     CreateContainerConfigError   0          11s
```

A trick!

The main `nginx` container image MUST run as the root user.  They do offer a version that can run as nonRoot: `nginxinc/nginx-unprivileged:mainline-alpine`

Update the image line in the deployment to be `nginxinc/nginx-unprivileged:mainline-alpine` and apply the deployment.

```yaml
    runAsNonRoot: true
  containers:
  - image: nginxinc/nginx-unprivileged:mainline-alpine
    name: k8s-standard
    ports:
...
```
```shell
$ kubectl apply -f 3-k8s-deploy.yaml
namespace/k8s-security unchanged
serviceaccount/k8s-security unchanged
deployment.apps/k8s-serviceaccount configured
```

Now check the status of the pod

```shell
kubectl get pods            
NAME                                  READY   STATUS    RESTARTS   AGE
k8s-serviceaccount-655967657f-26kxd   1/1     Running   0          26s
```


## Cleanup

Once done, you can remove the objects

```shell
$ kubectl delete  -f 3-k8s-deploy.yaml
namespace "k8s-security" deleted
serviceaccount "k8s-security" deleted
deployment.apps "k8s-serviceaccount" deleted
```