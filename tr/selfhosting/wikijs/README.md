## Mongo

### Docker

```yaml
version: "3"
services:

  db:
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: sifre
      POSTGRES_USER: dbadmin
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - /path/to/wikijs/db:/var/lib/postgresql/data
    networks:
      - wikijs
  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: dbadmin
      DB_PASS: sifre
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "8003:3000"
    networks:
      - wikijs
      - proxy
networks:
  wikijs:
    name: wikijs
  proxy:
    name: proxy
```
