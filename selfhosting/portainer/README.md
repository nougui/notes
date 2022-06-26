## Portainer

### Docker

```sh
docker run -d -p 8000:8000 -p 9443:9443 -p 9000:9000 \
--name portainer --restart=always \
-v /var/run/docker.sock:/var/run/docker.sock -v /path/to/portainer/data:/data \
cr.portainer.io/portainer/portainer-ce:latest
```

### Proxy
Connect Portainer to Proxy Network
```sh
docker network connect proxy portainer
```
