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
      nginx.ingress.kubernetes.io/enable-cors: 'true'
      nginx.ingress.kubernetes.io/backend-protocol: 'HTTPS'
      nginx.ingress.kubernetes.io/cors-allow-origin: "https://argocd.aimhighergg.com/"
      nginx.ingress.kubernetes.io/cors-expose-headers: '*, X-CustomResponseHeader'
      nginx.ingress.kubernetes.io/use-regex: 'true'
      whitelist-source-range: 192.168.1.1/24,${GLOBALIP}/32
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