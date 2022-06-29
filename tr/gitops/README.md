## gitops

```bash
curl -sfL https://get.k3s.io | sh -s - server \
--token=token \
--tls-san "$(curl ipinfo.io/ip)" \
--cluster-init
```

```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

```bash
sudo flux bootstrap github \
  --owner=owner \
  --repository=gitops \
  --branch=main \
  --path=clusters/cluster \
  --kubeconfig /etc/rancher/k3s/k3s.yaml \
  --personal
```

```bash
kubectl create secret generic git-https-credentials -n flux-system \
    --from-literal=username=kullanıcıadı \
    --from-literal=password=şifre
```

```bash
flux create source git helmcharts \
  --url=https://github.com/kullanıcıadı/helmcharts \
  --branch=main \
  --interval=30s \
  --export > ./clusters/k3s/podinfo-source.yaml
```
