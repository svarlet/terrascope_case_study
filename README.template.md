# Terrascope Case Study

## High-level diagram

```mermaid
architecture-beta
  group activity-ingestion(database)[Activities ingestion]
    service client-activity-queue(logos:aws-sqs)[Client activities queue] in activity-ingestion
    service client-activity-processor(logos:aws-lambda)[Client activity processor] in activity-ingestion
    service ai-matcher(cloud)[AI Matcher] in activity-ingestion
    service ef-results-queue(logos:aws-sqs)[EF results queue] in activity-ingestion
    service ef-results-processor(logos:aws-lambda)[EF result processor] in activity-ingestion
    service ef-matching-cache(logos:aws-dynamodb)[EF Matching Cache] in activity-ingestion

    junction ai-matcher-to-queue

    client-activity-queue:B --> T:client-activity-processor
    client-activity-processor:R --> L:ef-matching-cache
    client-activity-processor:L --> R:ai-matcher

    ai-matcher:B -- T:ai-matcher-to-queue
    ai-matcher-to-queue:R --> L:ef-results-queue
    ef-results-queue:R --> L:ef-results-processor
    ef-results-processor:T --> B:ef-matching-cache

  group backend(server)[Backend]
    service api-gateway(logos:aws-api-gateway)[API Gateway] in backend
    service lambdas(logos:aws-lambda)[Lambdas] in backend
    service auth(logos:aws-cognito)[Auth] in backend

    api-gateway:R -- L:lambdas
    lambdas{group}:B -- T:client-activity-queue
    lambdas:R -- L:auth

  group frontend(internet)[Frontend]
    service browser(logos:react)[React Frontend] in frontend
    service cdn(logos:aws-cloudfront)[CDN] in frontend
    service frontend-bucket(logos:aws-s3)[Frontend sources] in frontend

    browser:R -- L:cdn
    browser{group}:B -- T:api-gateway{group}
    cdn:R -- L:frontend-bucket

```
