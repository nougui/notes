## Mongo

### Docker

```yaml
version: '3.7'
services:
  mongodb_container:
    image: mongo:latest
    environment:
      MONGO_INITDB_ROOT_USERNAME: DBADMIN
      MONGO_INITDB_ROOT_PASSWORD: password
    ports:
      - 27017:27017
    volumes:
      - /path/to/mongo:/data/db
    networks:
      - mongo
      - proxy
networks:
  mongo:
    name: mongo
  proxy:
    name: proxy
```