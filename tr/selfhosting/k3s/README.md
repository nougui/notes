## K3s

### Kurulum

sunucu olarak k3s yükleyin

```sh
curl -sfL https://get.k3s.io | sh -s - server \
--token=token \
--tls-san "$(curl ipinfo.io/ip)" \
--cluster-init \
--disable local-storage
```

Otomatik oluşturulan jetonu alın

```sh
sudo cat /var/lib/rancher/k3s/server/node-token
```

Kümeye ana düğüm olarak katılın Yukarıdaki komuttan belirteci kullanın ve bu sunucular ip

```sh
curl -sfL https://get.k3s.io | sh -s - server \
--token=token \
--tls-san "$(curl ipinfo.io/ip)" \
--server https://10.0.0.101:6443 \
--disable local-storage
```

Yapılandırma dosyası

```bash
sudo cat /etc/rancher/k3s/k3s.yaml
```

Çıkarmak

```bash
/usr/local/bin/k3s-uninstall.sh
```
