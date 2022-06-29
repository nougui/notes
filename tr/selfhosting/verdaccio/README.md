## Verdaccio

### Docker

```yaml
version: '3.3'
services:
  verdaccio:
    image: verdaccio/verdaccio
    ports:
      - '4873:4873'
    volumes:
      - '/path/to/verdaccio/data:/opt/verdaccio'
      - '/path/to/verdaccio/conf:/verdaccio/conf'
      - '/path/to/verdaccio/storage:/verdaccio/storage'
      - '/path/to/verdaccio/plugins:/verdaccio/plugins'
    networks:
      - npm
      - proxy
networks:
  npm:
    name: npm
  proxy:
    name: proxy
```

```sh
sudo chown -R 10001:65533 /path/to/verdaccio
```
