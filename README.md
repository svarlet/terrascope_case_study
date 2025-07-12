# Terrascope Case Study

## High-level diagram

```mermaid
architecture-beta
  group frontend(internet)[Frontend]

  service browser(internet)[React Frontend] in frontend
  service cdn(logos:aws-s3)[Content Delivery Network] in frontend

  browser:B -- T:cdn

  group api(cloud)[API]

  service db(database)[Database] in api
  service disk1(disk)[Storage] in api
  service disk2(disk)[Storage] in api
  service server(server)[Server] in api

  db:L -- R:server
  disk1:T -- B:server
  disk2:T -- B:db
```
