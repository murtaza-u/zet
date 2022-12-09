# Use MetalLB in Minikube

Minikube comes with a 3rd party MetalLB addon. Enable it like any other
addon.

```bash
minikube addons enable metallb
```

However, unlike the Ingress addon, MetalLB does require one little
configuration.

```bash
kubectl edit configmaps config -n metallb-system
```

```yaml
apiVersion: v1
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.49.100-192.168.49.105 # <-------- look here
...
...
```

By default, the addresses are empty. You explicitly need to specify a
range of reserved IP addresses for the load balancers. It goes without
saying, the range of IP addresses should be within Minikube's subnet.

```bash
minikube ip

192.168.49.2
```

In my case, Minikube's subnet is 192.168.49.0/24. Hence I have reserved
the IPs 192.168.49.100 - 192.168.49.105 for load balancers.

    #k8s #minikube
