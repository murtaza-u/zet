# Load local image into Minikube

```bash
minikube image load local-image:latest
```

Make sure to set the `imagePullPolicy` to Never in your manifests. This
makes Kubernetes use the locally available image, if present.

```yaml
spec:
  containers:
  - name: my-container
    image: local-image:latest
    imagePullPolicy: Never
```


    #k8s #minikube
