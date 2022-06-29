## Watchtower

### Docker

```yaml
version: "3"
services:
  watchtower:
    image: containrrr/watchtower
    environment:
      - WATCHTOWER_NOTIFICATIONS=email
      - WATCHTOWER_NOTIFICATION_EMAIL_FROM=kimden@domain.com
      - WATCHTOWER_NOTIFICATION_EMAIL_TO=kime@domain.com
      - WATCHTOWER_NOTIFICATION_EMAIL_SERVER=smtp.domain.com
      - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PORT=587
      - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_USER=kimden@domain.com
      - WATCHTOWER_NOTIFICATION_EMAIL_SERVER_PASSWORD=sifre
      - WATCHTOWER_NOTIFICATION_EMAIL_DELAY=2
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 30
```
