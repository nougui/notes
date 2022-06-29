## Sunucu İlk Kurulum Adımları

### Uygulamalar

- [CertBot](certbot/README.md) - `SSL`Let's Encrypt Sertifikası Oluşturma
- [K3S](k3s/README.md) - `k8s` K3s
- [Mailjet](mailjet/README.md) - `Mail` Mailjet
- [MicroK8s](microk8s/README.md) - `k8s` Microk8s
- [Mongo](mongo/README.md) - `DB` Mongo Veritabanı
- [NetData](netdata/README.md) - `İzleme` Netdata
- [Nginx Proxy Manager](nginx-proxy-manager/README.md) - `Sunucu Yönetimi` Reverse Proxy Ngingx
- [Portainer](portainer/README.md) - `Sunucu Yönetimi` Docker Yöneticisi
- [Redis](redis/README.md) - `DB` Redis
- [Simple Login](simple-login/README.md) - `Mail` Geçici Posta
- [Syncthing](syncthing/README.md) - `Sync` Syncthing
- [Vaultwarden](vaultwarden/README.md) - `Şifre Yöneticisi` Daha Hafif Bitwarden
- [Verdaccio](verdaccio/README.md) - `NPM` Özel npm registry
- [WatchTower](watchtower/README.md) - `Sunucu Yönetimi` Docker imajlarını upgrade
- [Wikijs](wikijs/README.md) - `WIKI` Wikijs
- [Wireguard](wireguard/README.md) - `VPN` Wireguard Vpn

### Ubuntu

Apt

```sh
sudo apt update && \
sudo apt upgrade -y && \
sudo reboot
```

Snap

```sh
sudo apt install snapd -y
```

Root şifresini ayarlama

```sh
sudo passwd root
```

Ssh anahtarı kurma

```sh
sudo nano ~/.ssh/authorized_keys
```

### Docker

```sh
sudo apt-get update && \
sudo apt-get -y install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && \
sudo apt-get update && \
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

### Hosts

Dosyanın içini değiştir

```sh
sudo sed -i '/#start/,/#end/c\
#start\
10.0.0.103 node1 node1.domain.com\
10.0.0.149 node2 node2.domain.com\
10.0.0.88 node3 node3.domain.com\
10.0.0.87 node4 node4.domain.com\
10.0.0.176 node5 node5.domain.com\
10.0.0.28 node6 node6.domain.com\
35.238.128.200 node7 node7.domain.com\
#end' /etc/hosts
```

### Iptables

Hepsine izin ver

```sh
iptables -I INPUT -j ACCEPT
iptables -I OUTPUT -j ACCEPT
iptables -I FORWARD -j ACCEPT
iptables-save > /etc/network/iptables.rules
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

IP tablolarını kaydet

```sh
iptables-save > /etc/network/iptables.rules
```

Daha Güvenli Kurallar

```sh
iptables -I INPUT -p tcp --dport 80 -j ACCEPT -m comment --comment "HTTP e izin ver"
iptables -I INPUT -p tcp --dport 443 -j ACCEPT -m comment --comment "HTTPS e izin ver"
iptables -I INPUT -p tcp -m state --state NEW --dport 22 -j ACCEPT -m comment --comment "SSH e izin ver"
iptables -I INPUT -p tcp --dport 8071:8079 -j ACCEPT -m comment --comment "Torrentlere izin ver"
```

```sh
sudo iptables -F && sudo iptables-save | sudo tee /etc/iptables/rules.v4
```
