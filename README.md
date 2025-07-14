# Terrascope Case Study

## High-level diagram

![diagram](./mermaid/README-1.svg)

## Project delivery

### Principles

Rooted in Agile principles and a Product Mindset, the following customer-centric delivery plan aims to slice feature development to achieve small-yet-frequent value-add deliveries.

The Product Team and the customers will collaborate _directly_ to codevelop the product by prioritizing value-adds, and tweaking, reworking, or pivoting early until efforts achieve customer success. We will avoid information relays or silos, sometimes observed when the Product Team is not directly connected to the customers. Doing so, we aim to engage the Product Team beyond coding, and engage customers in building the product they love, thereby fostering Employees and Customers retention.

### Sliced delivery plan

#### Delivery #1

**Objective**

Start onboarding customers. Evaluate the UX of file uploads. Discover the heterogeneity of customers' data.

**Key product increments**

- Simple frontend with `Basic` Auth, activities upload form;
- API to upload activities as a CSV file and log individual activities;
- S3 bucket to store uploaded activities;
- Terraform scripts;

#### Delivery #2

**Objective**

Early AI Matcher discovery: discover challenges, risks, API differences, etc.

**Key product increments**

- Activities are individually pushed to the AI Matcher through an SQS queue with a unique lambda listener (concurrency = 1)
- AI Matcher results queued with SQS and a unique lambda listener who records the activity, its emission factor, and its emissions to the database
