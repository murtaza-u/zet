# Caveat to mounting individual config map entries as files

Mounting individual config map entries as files doesn't hide files in
that directory as contrast to mounting the entire config map as a
directory. However, there is one caveat to this: the mounted files are
not updated when the config map values are changed. The pods in question
needs to be restarted for the mounted files to be updated.

Below is an example of a custom nginx config file mounted as an
individual file at `/etc/nginx/conf.d/gzip.conf` in the container.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig
data:
  mynginx.conf: |
    # my custom nginx configuration

    server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /usr/share/nginx/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        gzip on;

        location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to displaying a 404.
            try_files $uri $uri/ =404;
        }
    }
```

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: mount
        image: nginx:latest
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: config
          ## mounting entire directory
          # mountPath: /etc/nginx/conf.d
          ##

          ## mounting a single file
          mountPath: /etc/nginx/conf.d/gzip.conf
          subPath: mynginx.conf
          ##
      volumes:
        - name: config
          configMap:
            name: myconfig
      restartPolicy: Always
---
```

```bash
kubectl exec -it mount-5d4ffd47b8-rrjls -- /bin/bash

root@mount-5d4ffd47b8-rrjls:/# ls -l /etc/nginx/conf.d/
total 8
-rw-r--r-- 1 root root 1093 Aug 27 06:51 default.conf
-rw-r--r-- 1 root root  460 Aug 27 06:51 gzip.conf
```

Had the config map be mounted as a directory, the `default.conf` file
would be invisible.

Now go ahead and make changes to the `mynginx.conf` value, you'll notice
the `gzip.conf` file is not updated.

```bash
kubectl edit configmaps myconfig
```


    #Kubernetes #ConfigMaps #Volumes
