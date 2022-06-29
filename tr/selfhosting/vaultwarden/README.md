## VaultWarden

### Docker

```yaml
version: "3"
services:
  vaultwarden :
    image: 'vaultwarden/server:latest'
    restart: unless-stopped
    ports:
      - '8002:80'
    environment:
      - SMTP_HOST=smtp.domain.com
      - SMTP_FROM=noreply@domain.com
      - SMTP_PORT=587
      - SMTP_SSL=true
      - SMTP_USERNAME=SMTP_KULLANICI
      - SMTP_PASSWORD=SMTP_SIFRE
      - DOMAIN=https://vaultwarden.domain.com
      - ADMIN_TOKEN=some_long_token_for_admin_some_long_token_for_admin_some_long_to
    volumes:
      - /path/to/passwordmanager/vw-data:/data
    networks:
      - vaultwarden
      - proxy
networks:
  vaultwarden:
    name: vaultwarden
  proxy:
    name: proxy
```
