# ingress-aggregator
Aggregate and manage ingress

# Example(access control)

```
cd overlay/argocd
```
```
GLOBALIP=1.1.1.1
```
```
vi kustomization.yaml
```
```
patchesStrategicMerge:
- |-
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-tls
    annotations:
      kubernetes.io/tls-acme: "true"
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
      nginx.ingress.kubernetes.io/ssl-passthrough: "true"
      ingress.kubernetes.io/ssl-redirect: "false"
      nginx.ingress.kubernetes.io/whitelist-source-range: "${GLOBALIP}, 192.168.1.0/24"
  spec:
    ingressClassName: nginx
    tls:
    - hosts:
        - argocd.aimhighergg.com
      secretName: tls-secret
    rules:
    - host: argocd.aimhighergg.com
      http:
        paths:
        - pathType: Prefix
          path: /
          backend:
            service:
              name: argocd-server
              port:
                number: 8080
        - pathType: Prefix
          path: /login(.*)
          backend:
            service:
              name: argocd-server
              port:
                number: 8080
```
```
kubectl apply -k
```

# Note

If your backend is an HTTPS or HTTP protocol, you'll also need to change the ingress annotations accordingly.
バックエンドが HTTPS か HTTP protocol 場合、 ingress の annotations もそれに合わせて変更する必要があります。
```
metadata:
  annotations:
      nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```
```
metadata:
  annotations:
      nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
```