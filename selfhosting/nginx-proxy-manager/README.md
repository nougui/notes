## Proxy

### Docker

```yaml
version: "3"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '443:443'
      - '81:81'
    environment:
      DB_SQLITE_FILE: "/data/database.sqlite"
    volumes:
      - /path/to/nginx-proxy-manager/data:/data
      - /path/to/nginx-proxy-manager/letsencrypt:/etc/letsencrypt
    networks:
      - proxy
networks:
  proxy:
    name: proxy
```

Default email: 
```
admin@example.com
```
Default password: 
```
changeme
```