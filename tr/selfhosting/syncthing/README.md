## SYNCTHING

### Ubuntu

Kurulum

```sh
curl -s https://syncthing.net/release-key.txt | sudo apt-key add -
echo "deb https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
sudo apt-get update && \
sudo apt-get install apt-transport-https -y && \
sudo apt-get update && \
sudo apt-get install syncthing -y
```

Dosyaları Test Et ve Oluştur

```sh
syncthing
```

Systemd kaydı oluşturma

```sh
sed -i 's/127.0.0.1:8384/0.0.0.0:8384/g' ~/.config/syncthing/config.xml
systemctl enable syncthing@$USER.service
systemctl start syncthing@$USER.service
systemctl status syncthing@$USER.service
```
