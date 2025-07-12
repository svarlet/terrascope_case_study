# Terrascope Case Study

## High-level diagram

```mermaid
architecture-beta
  group frontend(internet)[Frontend]

  service frontend(internet)[Frontend (React)] in frontend
  service cdn(logos:aws-cloudfront)[Content Delivery Network (CDN)] in frontend
  frontend:B -- T:cdn

  group api(cloud)[API]

  service db(database)[Database] in api
  service disk1(disk)[Storage] in api
  service disk2(disk)[Storage] in api
  service server(server)[Server] in api

  db:L -- R:server
  disk1:T -- B:server
  disk2:T -- B:db
```
