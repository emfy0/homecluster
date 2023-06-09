# K3S cluster

Configuring private registries

Note that your k3s cluster needs a configuration file `/etc/rancher/k3s/registries.yaml` for the authentication.
```
mirrors:
  ghcr:
    endpoint:
      - "https://ghcr.io"
configs:
  "ghcr.io":
    auth:
      username: $YOUR_USERNAME
      password: $YOUR_PRIVATE_ACCESS_TOKEN
```

# Cert manager
see cert manager yaml

Apply the manifest
```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```
```
kubectl get pods --namespace cert-manager
```
```
cat letsencrypt-prod.yaml | envsubst | kubectl apply -f -
```


```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: ${EMAIL}
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: traefik
```

```
cat traefik-https-redirect-middleware.yaml | envsubst | kubectl apply -f -
```
```
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: redirect-https
spec:
  redirectScheme:
    scheme: https
    permanent: true
```  
```
cat ./whoami/whoami-ingress-tls.yaml | envsubst | kubectl apply -f -
```
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: whoami-tls-ingress
  annotations:
    kubernetes.io/ingress.class: traefik
    cert-manager.io/cluster-issuer: letsencrypt-prod
    traefik.ingress.kubernetes.io/router.middlewares: default-redirect-https@kubernetescrd
spec:
  rules:
    - host: whoami.${DOMAIN}
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: whoami
                port:
                  number: 5678
  tls:
    - secretName: whoami-tls
      hosts:
        - whoami.${DOMAIN}
```

https://k3s.rocks/https-cert-manager-letsencrypt/

https://github.com/tzumby/rails-on-kubernetes?ref=monkeyvault.net


creating secrets
```
$ kubectl create -n <...> secret generic db-user-pass --from-literal=password=mysecretpass
```
```
$ kubectl create -n <...> secret generic db-user --from-literal=username=postgres
```
```
$ kubectl create secret generic secret-key-base --from-literal=secret-key-base=50dae16d7d1403e175ceb2461605b527cf87a5b18479740508395cb3f1947b12b63bad049d7d1545af4dcafa17a329be4d29c18bd63b421515e37b43ea43df64
```

traefik service
```
externalIPs:
    - externalip1
    - externalip2
  sessionAffinity: None
  externalTrafficPolicy: Local
```
