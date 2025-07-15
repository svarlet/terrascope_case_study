# Terrascope Case Study

## High-level diagram

![diagram](./mermaid/README-1.svg)

## Project delivery

### Principles

Rooted in Agile principles and a Product Mindset, the following customer-centric delivery plan aims to slice feature development to achieve small-yet-frequent value-add deliveries.

The Product Team and the customers will collaborate _directly_ to codevelop the product by prioritizing value-adds, and tweaking, reworking, or pivoting early until efforts achieve customer success. We will avoid information relays or silos, sometimes observed when the Product Team is not directly connected to the customers. Doing so, we aim to engage the Product Team beyond coding, and engage customers in building the product they love, thereby fostering Employees and Customers retention.

NB. The plan that follows should be challenged and rearranged in the face of market changes, customer feedback, or a shift in business priorities. It represents an optimistic strategy to construct the product from scratch.

### Sliced delivery plan

#### 🚀 Delivery #1

**Objective**

Start onboarding customers. Evaluate the UX of file uploads. Discover the heterogeneity of customers' data.

**Key product increments**

- Simple frontend with `Basic` Auth, activities upload form;
- API to upload activities as a CSV file and log individual activities;
- S3 bucket to store uploaded activities;
- Terraform scripts;
- Continuous delivery with Github Actions

#### 🚀 Delivery #2

**Objective**

Early AI Matcher discovery: discover challenges, risks, API differences, etc.

**Key product increments**

- Activities are individually pushed to the AI Matcher through an SQS queue with a unique lambda listener (concurrency = 1)
- AI Matcher results queued with SQS. A unique lambda listener calculates the activity emissions and records them in the DB
- AI Matcher failures forwarded to a dead letter queue.
- Frontend shows a paginated table of the activities and their respective emissions

#### 🚀 Delivery #3

**Objective**

Enable the system to scale considering the AI Matcher performance and scale constraints

**Key product increments**

- Enable up to 10 concurrent requests to the AI matcher
- Trigger the AI Matcher when the activity has no reusable historical data (a form of caching with our database)
- Enable concurrent processing of AI Matcher results

#### 🚀 Delivery #4

**Objective**

Near real time visibility on health of the infrastructure.

**Key product increments**

- Monitoring dashboard with Cloudwatch
- Thresholds defined (queues, dead-letter queues, lambda invocations, latencies, cpu/memory usage)
- Alarms set up

#### 🚀 Delivery #5

**Objective**

Isolate customers and upgrade authentication.

**Key product increments**

- Single tenant infrastructure (dedicated AWS infrastructure per tenant). Terraform scripts refactored accordingly.
- AWS Cognito to support user authentication

#### 🚀 Delivery #6

**Objective**

Distinct user accounts (accountants and viewers) with adequate authorizations.

**Key product increments**

- Accountant user type can upload and view activities
- Viewers can view activities

## Technical implementation

### Key API endpoints

#### API Versionning

We introduce API versionning now, not because multiple API versions are already designed but because now is the easiest time to account for multiple API versions in our API design. We are using a simple, REST-friendly, and cache-friendly "URI-based versioning" strategy where the version is specified in a URI path. For exanple:

> GET /v1/home

#### Common errors

| HTTP Status code | Reason |
|------------------|--------|
| 400 | Invalid request. The payload or the JWT token cannot be parsed. |
| 401 | Unauthorized. The JWT token is missing or has expired. |
| 403 | The authenticated user lacks sufficient privileges to access the requested resource. Do they have the adequate role? |
| 404 | The resource does not exist. |

#### Authentication

##### POST /v1/auth/login

**Request payload**

```json
{
  "credentials": {
    "login": string,
    "password": string
  }
}
```

**Response**

When signing in succeeds, the API responds with a 200 status code and a JWT token in the payload. The JWT encodes the user's unique identity as a `uuid` and its `role` ("accountant" or "viewer")

```json
{
  "jwt": string
}
```

##### GET /v1/me

**Request headers**

```
Authorization: Bearer <jwt token>
```

**Response**

When the JWT is valid (correct structure and hasn't expired), the API responds with a 200 status code and a payload with information about the signed in user.

```json
{
  "identity": {
    "firstname": string,
    "lastname": string,
  },
  "email": string,
  "preferences": {
    "marketing_opted_in": bool,
    time_format: 12 | 24,
    timezone: string
  }
}
```

#### Home page

##### GET /v1/home

**Request headers**

```
Authorization: Bearer <jwt token>
```

**Response**

When the JWT is valid (correct structure and it hasn't expire), the API responds with a 200 status code and a payload with data to render on the home page for the signed in user:

```json
{
  "announcement": string, // upcoming release, scheduled maintenance, etc.
  "product_tips" : [ // illustrative - a list of product tips to display
    {
      "title": string,
      "description": string,
      "permalink": url
    }
  ]
}
```

#### System data

##### GET /v1/system/essentials

This endpoint provides essential information about the system to any user, regardless of their authentication state.

**Response**

```json
{
  "help_url": url,
  "support_email": email,
  "version": string,
}
```

##### GET /v1/system/feature_flags

This endpoint provides the list of feature-flags enabled for the authenticated user. Flags may not be enable to all users as they might require a specific `role`
r be assigned by an A/B testing policy depending on the user's cohort.

**Request headers**

```
Authorization: Bearer <jwt token>
```

**Response**

Assuming a valid jwt token (structure and expiry check), the endpoint responds with a 200 status code and a list of flags enabled for the authenticated user.

```json
[string]
```

**Example**

```json
[
  "product_tips", // product tips can be displayed
  "dark_theme_selector" // the dark theme selector can be displayed
]
```

At the same moment, another user may get the following feature flags:

```json
[
  "product_tips"
]
```
