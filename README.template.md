# Terrascope Case Study

## High-level diagram

```mermaid
architecture-beta
  group backend(server)[Backend]
    service api-gateway(logos:aws-api-gateway)[API Gateway] in backend
    service lambdas(logos:aws-lambda)[Lambdas] in backend
    api-gateway:B -- T:lambdas

  group frontend(internet)[Frontend]
    service browser(logos:react)[React Frontend] in frontend
    service cdn(logos:aws-cloudfront)[CDN] in frontend
    browser:B -- T:cdn
    browser:R -- L:api-gateway{group}

```
