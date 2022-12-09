# Replicating gitRepo volume type behaviour using init container

As of Kubernetes version 1.11, the `gitRepo` volume type is deprecated.
[Kubernetes](<https://kubernetes.io/docs/concepts/storage/volumes/#gitrepo>)
recommends to use init container to replicate the behaviour instead.

> The gitRepo volume type is deprecated. To provision a container with a
> git repo, mount an EmptyDir into an InitContainer that clones the repo
> using git, then mount the EmptyDir into the Pod's container.

Below is an example of a nginx web server serving static files from a
volume named `html`. An init container is used to clone the static files
from a remote git repository into the mounted volume.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: default
  labels:
    app: web
spec:
  selector:
    matchLabels:
      app: web
  replicas: 1
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: nginx:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
      initContainers:
      - name: clone
        image: bitnami/git
        imagePullPolicy: IfNotPresent
        command:
          - sh
          - -c
          - git clone https://github.com/murtaza-u/k8s-vol-git-repo /repo
        volumeMounts:
        - name: html
          mountPath: /repo
      volumes:
        - name: html
          emptyDir: {}
      restartPolicy: Always
---
```

    #k8s
