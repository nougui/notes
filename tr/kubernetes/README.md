## Kubernetes

### Nginx

```sh
git clone https://github.com/nginxinc/kubernetes-ingress/
```

```sh
cd kubernetes-ingress/deployments/helm-chart
```

```sh
git checkout v2.1.2
```

```sh
helm repo add nginx-stable https://helm.nginx.com/stable
```

```sh
helm repo update
```

```sh
helm install my-release nginx-stable/nginx-ingress
```

### ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

```sh
sudo kubectl apply -f user.yaml
```

```sh
sudo kubectl -n kube-system describe secret $(sudo kubectl -n kube-system get secret | grep admin-user | cut -d " " -f1)
```

```sh
sudo kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

```sh
sudo kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml
```

### longhorn

Temel http kimlik doğrulaması

```bash
printf "admin:`openssl passwd -apr1`\n" >> http-basic-auth
```

```
password
```

```bash
sudo kubectl create secret generic http-basic-auth --from-file http-basic-auth --namespace longhorn-system
```

```bash
kubectl create secret generic http-basic --from-file http-basic-auth --namespace longhorn-system
```

Longhorn ingress

```yaml
---
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: http-basic-auth-middleware
  namespace: longhorn-system
spec:
  basicAuth:
    removeHeader: true
    secret: http-basic-auth
---
apiVersion: v1
kind: Secret
metadata:
  name: longhorn-system-ssl
  namespace: longhorn-system
type: kubernetes.io/tls
data:
  tls.crt: <>
  tls.key: <>
---
kind: Ingress
apiVersion: networking.k8s.io/v1
metadata:
  name: longhorn-ingress
  namespace: longhorn-system
  annotations:
    kubernetes.io/ingress.class: traefik
    traefik.ingress.kubernetes.io/router.middlewares: longhorn-system-http-basic-auth-middleware@kubernetescrd
spec:
  tls:
    - hosts:
        - https-longhorn.domain.com
      secretName: longhorn-system-ssl
  rules:
    - host: longhorn.domain.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: longhorn-frontend
                port:
                  number: 80
---
```

scaleway secret

```bash
kubectl create secret generic scw-secret \
    --from-literal=AWS_ACCESS_KEY_ID=ACCESS_KEY \
    --from-literal=AWS_SECRET_ACCESS_KEY=SECRET_ACCESS_KEY \
    --from-literal=AWS_ENDPOINTS=https://s3.nl-ams.scw.cloud \
    --from-literal=AWS_REGION=nl-ams \
    -n longhorn-system
```
