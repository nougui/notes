## Redis

### Docker

```yaml
version: '3.3'
services:
  redis:
    image: redis
    ports:
      - '6379:6379'
    command: redis-server --save 20 1 --loglevel warning --requirepass sifre
    volumes:
      - /path/to/redis:/data
    networks:
      - redis
      - proxy
networks:
  redis:
    name: redis
  proxy:
    name: proxy
```
