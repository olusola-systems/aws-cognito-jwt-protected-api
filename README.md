# Week 22 — Securing an Image Metadata API with OAuth 2.0 JWT Authentication

## Overview

This project completed the authentication workflow for a serverless Image Metadata API by implementing Amazon Cognito OAuth 2.0 token exchange and JWT-based authorization.

Building on the previous project, I exchanged an authorization code for JWT tokens using Amazon Cognito's OAuth 2.0 token endpoint. I then used the access token to authenticate requests against an Amazon API Gateway HTTP API protected by a JWT Authorizer. Only authenticated requests were allowed to reach the backend Lambda function and retrieve image metadata stored in Amazon DynamoDB.

By the end of the project, I successfully completed the full authentication lifecycle from user sign-in through authorized API access.

---

## AWS Services Used

- Amazon Cognito
- Amazon API Gateway (HTTP API)
- AWS Lambda
- Amazon DynamoDB
- Amazon S3
- AWS IAM
- Postman

---

## Architecture

<img width="2880" height="1800" alt="Architecture" src="https://github.com/user-attachments/assets/eb7f150b-36bd-4647-b026-207cb6f05431" />

---

## What I Built

**OAuth 2.0 Token Exchange**

Generated a new authorization code through Amazon Cognito Managed Login and exchanged it for JWT tokens using the Cognito `/oauth2/token` endpoint.

Configured:
- Authorization Code Grant
- x-www-form-urlencoded request
- Cognito Token Endpoint
- Client ID
- Redirect URI
- Authorization Code

Successfully received:
- Access Token
- ID Token
- Refresh Token

---

**JWT Authentication**

Configured Postman to authenticate using the Cognito access token.

Added:
- Bearer Token authorization
- Cognito Access Token

Verified that authenticated requests were accepted by API Gateway.

---

**Protected API Access**

Sent authenticated requests to the Image Metadata API using Postman.

Verified that:
- JWT Authorizer validated the token
- API Gateway authorized the request
- Lambda executed successfully
- DynamoDB returned stored image metadata
- API responded with HTTP 200 OK

---

**End-to-End Authentication Workflow**

Successfully completed the complete authentication process:

1. User authenticated through Amazon Cognito
2. Cognito generated an Authorization Code
3. Authorization Code exchanged for JWT Tokens
4. Access Token added to Postman
5. API Gateway validated the JWT
6. Lambda executed
7. DynamoDB returned image metadata

---

## Challenges

**Challenge 1 — Authorization Code Expired**

Problem: The token endpoint returned an `invalid_grant` error during token exchange.

Cause: Amazon Cognito authorization codes are single-use and expire after a short period. I attempted to exchange an authorization code that had already expired.

Resolution: Generated a fresh authorization code through the Managed Login page and immediately exchanged it for JWT tokens. The request completed successfully and returned the expected access, ID, and refresh tokens.

---

**Challenge 2 — Incorrect Bearer Token Format in Postman**

Problem: After successfully exchanging the authorization code for JWT tokens, authenticated API requests continued to fail even though a valid access token had been generated.

Cause: I mistakenly pasted the entire token field into Postman, including the text `"access_token":` and the surrounding quotation marks, instead of copying only the JWT value. As a result, API Gateway received an improperly formatted Bearer token and could not validate it.

Resolution: Copied only the raw JWT string from the `access_token` field — without the `"access_token":` label or quotation marks — and pasted it into Postman's Bearer Token field. After correcting the token format, API Gateway successfully validated the JWT, the request reached the Lambda function, and the API returned an HTTP 200 OK response with the image metadata.

---

**Challenge 3 — Matching Cognito App Clients**

Problem: The API continued rejecting valid tokens during troubleshooting.

Cause: The JWT Authorizer validates the audience (`aud`) claim against the configured Cognito App Client. Using a token issued for a different App Client would prevent authorization even if the token itself was valid.

Resolution: Verified that the Cognito App Client used for token generation matched the audience configured in the API Gateway JWT Authorizer before testing again.

---

## Lessons Learned

This project reinforced that OAuth 2.0 is a complete authentication workflow rather than a single configuration step. Successfully protecting an API required understanding how Amazon Cognito, the authorization code grant, token exchange, JWT validation, API Gateway, and Lambda all work together to establish trust between the client and backend services.

One of the most valuable lessons came from working with authorization codes. I learned that these codes are intentionally short-lived and can only be exchanged once. Encountering the `invalid_grant` error initially suggested a configuration problem, but it ultimately reinforced the importance of understanding OAuth security behavior instead of assuming every error indicates a broken service.

I also gained a deeper understanding of JWT tokens. Rather than treating the token as an opaque string, I recognized that API Gateway validates the token's issuer, audience, expiration, and signature before allowing requests to reach the backend. This demonstrated how authorization decisions are enforced before Lambda code is ever executed.

Finally, this project highlighted the value of systematic troubleshooting. Working through each stage independently — from generating a fresh authorization code, exchanging it for tokens, configuring Postman correctly, and validating API Gateway authorization — made it much easier to isolate issues and confirm that every component of the authentication pipeline was functioning as expected.

---

## Skills Demonstrated

- Amazon Cognito
- OAuth 2.0 Authorization Code Grant
- JWT Authentication
- API Gateway JWT Authorizers
- Secure API Design
- AWS Lambda
- Amazon DynamoDB
- Amazon S3
- IAM
- HTTP APIs
- Postman API Testing
- OAuth Token Exchange
- Authentication Workflow Troubleshooting

---

## Screenshots

### Cognito Managed Login Page

<img width="2880" height="1800" alt="Managed Login Page" src="https://github.com/user-attachments/assets/3b3f7402-ac33-497f-946e-dc73ae5d52e0" />

### Successful Authorization Code Redirect

<img width="2880" height="1800" alt="Successful Authorization Code Redirect" src="https://github.com/user-attachments/assets/82243077-ab6a-41e9-adee-786f817e2481" />

### Postman POST Request with Successful Token Response

<img width="2880" height="1800" alt="Postman POST Request" src="https://github.com/user-attachments/assets/de0bf825-a605-4066-896a-b665cc78e773" />

### API Gateway JWT Authorizer Attached to GET /images Route

<img width="2880" height="1800" alt="API Gateway JWT Authorizer Attached to GET _images Route" src="https://github.com/user-attachments/assets/026ea531-d2f8-453a-a713-a0e624544d16" />


### Postman GET Request with Bearer Token, Successful HTTP 200 API Response, and Returned Image Metadata JSON

<img width="2880" height="1800" alt="Postman GET request" src="https://github.com/user-attachments/assets/1d77c789-3984-441b-8a60-6b0a9c0cb0a7" />

---

## Outcome

Successfully completed an end-to-end OAuth 2.0 authentication workflow for a serverless Image Metadata API. Using Amazon Cognito, I authenticated users through Managed Login, exchanged authorization codes for JWT tokens, and used the access token to securely access an API protected by an API Gateway JWT Authorizer. Authenticated requests successfully reached Lambda and returned image metadata from DynamoDB, demonstrating a complete implementation of secure API authentication and authorization.

---

## Next Project

Week 23 — Uploading Images Through an Authenticated API

The next project will extend the authenticated API by implementing secure image uploads. Authenticated users will upload images to Amazon S3 through API Gateway and AWS Lambda, while storing metadata in DynamoDB. This project will build on the authentication foundation established in Weeks 21 and 22 by enforcing authorization on both read and write operations.
