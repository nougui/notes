## Microk8s

Setup microk8s

```sh
sudo snap install microk8s --classic
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
su - $USER
```

Setup add-ons

```sh
microk8s enable dashboard dns registry ingress && \
microk8s enable metallb:$(curl ipinfo.io/ip)-$(curl ipinfo.io/ip)
```

Get dashboard login token

```sh
microk8s kubectl -n kube-system describe secret $(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
```

Open dashboard proxy

```sh
microk8s dashboard-proxy
```

Get kube config

```sh
microk8s config
```

Edit ingress

```sh
microk8s kubectl edit -n  ingress daemonset.apps/nginx-ingress-microk8s-controller
```

###Multi Node Cluster

Add Node

```sh
microk8s add-node
```

Outputs:

```
From the node you wish to join to this cluster, run the following:
microk8s join 1.2.3.4:25000/372c70dfaed2962813800814236de75b/a513a4674c9c

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 1.2.3.4:25000/372c70dfaed2962813800814236de75b/a513a4674c9c --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 1.2.3.4:25000/372c70dfaed2962813800814236de75b/a513a4674c9c
```

Join Node

```sh
microk8s join node1.domain.com:25000/372c70dfaed2962813800814236de75b/a513a4674c9c
```

Join Node As Worker

```sh
microk8s join node1.domain.com:25000/372c70dfaed2962813800814236de75b/a513a4674c9c --worker
```

Leave Node

```sh
microk8s leave
```

Remove Node

```sh
microk8s remove-node node2
```

```sh
microk8s remove-node node2 --force
```

Add nodes to host

```sh
sudo cat << EOF | sudo tee -a /etc/hosts
$(dig node1.domain.com +short | tail -1) node1
$(dig node2.domain.com +short | tail -1) node2
$(dig node3.domain.com +short | tail -1) node3
EOF
```

Refresh Cert
```sh
sudo microk8s.refresh-certs
```

Change Cert Template
```sh
sudo nano /var/snap/microk8s/current/certs/csr.conf.template
```
```
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster
DNS.5 = kubernetes.default.svc.cluster.local
DNS.100 = node1
DNS.101 = node1.domain.com
IP.1 = 127.0.0.1
IP.2 = 10.152.183.1
IP.100 = 141.147.2.129
```

Replace inside of file
```sh
sudo sed -i '/#start/,/#end/c\
#start\
10.0.0.103 node1 node1.domain.com\
10.0.0.149 node2 node2.domain.com\
10.0.0.88 node3 node3.domain.com\
10.0.0.87 node4 node4.domain.com\
10.0.0.81 node5 node5.domain.com\
10.0.0.28 node6 node6.domain.com\
35.223.166.107 node7 node7.domain.com\
#end' /etc/hosts
```


Replace inside of file
```sh
sudo sed -i '/#start/,/#end/c\
#start\
141.147.7.191 node1 node1.domain.com\
132.226.193.176 node2 node2.domain.com\
158.101.165.114 node3 node3.domain.com\
132.226.192.12 node4 node4.domain.com\
141.144.249.251 node5 node5.domain.com\
144.24.169.134 node6 node6.domain.com\
10.128.0.12 node7 node7.domain.com\
#end' /etc/hosts
```
Change Worker Traefik
```sh
sudo echo -e "tcp:\n\
  routers:\n\
    Router-1:\n\
      rule: HostSNI(\`*\`)\n\
      service: kube-apiserver\n\
      tls:\n\
        passthrough: true\n\
  services:\n\
    kube-apiserver:\n\
      loadBalancer:\n\
        servers:\n\
        - address: node1:16443\n\
        - address: node2:16443\n\
        - address: node3:16443\n\
        - address: node4:16443\n\
        - address: node5:16443\n\
        - address: node6:16443\n\
        - address: node7:16443\n"  > /var/snap/microk8s/current/args/traefik/provider.yaml
```
