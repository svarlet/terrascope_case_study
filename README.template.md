# Terrascope Case Study

## High-level diagram

```mermaid
architecture-beta
  group backend(server)[Backend]
  service api-gateway(logos:aws-api-gateway)[Endpoints] in backend
  service lambdas(logos:aws-lambda)[Endpoint executors] in backend

  group frontend(internet)[Frontend]
  service browser(logos:react)[React Frontend] in frontend
  service cdn(logos:aws-cloudfront)[CDN] in frontend
  browser:B -- T:cdn
  browser:R -- L:api-gateway{group}

```
