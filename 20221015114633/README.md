# Static pods - the solution to Kubernetes chicken-and-egg problem

* Static pods are managed directly by the Kubelet daemon.
* The API server does *not* observe static pods. This means that static
  pods are independent of all control plane components (other than the
  kubelet) and thus can be started (and are usually started) before the
  cluster itself.
* The Kubelet does however create a mirror pod on the Kubernetes API
  server for each static pod, allowing them to be visible on the API
  server, but cannot be controlled from there. The pod names will be
  suffixed with the node's hostname("control" in the example below) with
  a leading hyphen.

```
NAMESPACE        NAME                              READY   STATUS
kube-system      etcd-control                      1/1     Running
kube-system      kube-apiserver-control            1/1     Running
kube-system      kube-controller-manager-control   1/1     Running
kube-system      kube-scheduler-control            1/1     Running
```

* `kubeadm init` spins up 4 static pods - etcd, apiserver, controller &
  scheduler in the `kube-system` namespace. Since these components are
  what make up the control plane, they need to run as static pods.
* You most likely would never have to deal with static pods directly,
  unless you're deploying a high availability cluster using `kube-vip` or
  similar.

## Related

* <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>
* <https://kube-vip.io/>
* <https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md>

    #k8s
