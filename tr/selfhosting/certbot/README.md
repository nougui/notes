## Certbot

```sh
docker run -it --rm -v /etc/letsencrypt:/etc/letsencrypt certbot/certbot certonly --manual -d *.domain.com
```
