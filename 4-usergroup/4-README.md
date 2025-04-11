# Standard Kuberentes Deployment with runAsUser and runAsGroup

## Why specify runAsUser and runAsGroup


* https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-pod
* https://stackoverflow.com/questions/73358804/where-did-kubernetes-security-context-runasuser-1000-come-from


From the StackOverflow documenation
>Remmeber that `runAsUser` and `runAsGroup` ensures container processes do not run as the root user but don’t rely on the `runAsUser` or `runAsGroup` settings to guarantee this. Be sure to also set `runAsNonRoot: true`

### How to find the user and group id of a container

Check the documentation of the image on Dockerhub or Github.

Launch the container locally and run `id` to see what returns


## Standard Deployment with runAsUser

Apply this standard Kubernetes Deployment using the `nginxinc/nginx-unprivileged:mainline-alpine` image running with specific user and group. We are using the `nginx-unprivileged` image because it does not use a root account and the documentation states `uid 100` and `gid 100`. 


```yaml
  spec:
    serviceAccountName: k8s-security
    automountServiceAccountToken: false
    securityContext:
      runAsUser: 101
      fsGroup: 101
    containers:
...
```

If you use the `nginx` image, it will not work and have errors in the log.  *Try it for fun and learning!*

### Deploy

This creates a namespace, a service account and 1 pod deployment running the `nginx:mainline` image with `runAsUser` and `runAsGroup` set


```shell
$ kubectl apply -f 4-k8s-deploy.yaml
namespace/k8s-security created
serviceaccount/k8s-security created
deployment.apps/k8s-serviceaccount created
```


### View user and group id

Confirm that the container is still running as `uid 100` and `gid 100`

```shell
$ kubectl exec -it <pod>> -- id
uid=101(nginx) gid=101(nginx) groups=101(nginx)
```


## Cleanup

Once done, you can remove the objects

```shell
$ kubectl delete -f 4-k8s-deploy.yaml
namespace "k8s-security" deleted
serviceaccount "k8s-security" deleted
deployment.apps "k8s-serviceaccount" deleted
```