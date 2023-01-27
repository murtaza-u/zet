# Mutual TLS(mTLS) and gRPC with Kubernetes Nginx ingress

gRPC supports mutual TLS out of the box. To configure Nginx ingress to
support mutual TLS, you need to,

* Create a TLS secret of type `kubernetes.io/tls` to store the server's
  certificate and private key.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: apisrv-tls
  namespace: default
type: kubernetes.io/tls
data:
  tls.crt: |
    ....
  tls.key: |
    ....
```

* Create another secret `ca-secret` of type `Opaque` to store the CA's
  certificate. The key must strictly be equal to `ca.crt`, Nginx ingress
  looks up for this key while setting up mutual TLS.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: ca-secret
  namespace: default
type: Opaque
data:
  # do not change the key name
  ca.crt: |
    ....
```

* Lastly add the following annotations to your ingress manifest

```yaml
  annotations:
    nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
    nginx.ingress.kubernetes.io/auth-tls-secret: default/ca-secret # the secret we created in the previous step
    nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
    nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
```

Complete example ingress manifest,

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apisrv
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream: "true"
    nginx.ingress.kubernetes.io/auth-tls-secret: default/ca-secret
    nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
    nginx.ingress.kubernetes.io/auth-tls-verify-depth: "1"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
spec:
  tls:
  - hosts:
      - apisrv.example.com
    secretName: apisrv-tls
  rules:
  - host: apisrv.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: apisrv
            port:
              number: 2023
---
```


  #k8s #grpc
