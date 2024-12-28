## Intro

- Used to build `mobile and web applications`
- `amplify init` - initialize an amplify project
- Provides features such as data storage, authentication, ML, etc. (all powered by AWS services)
- Provides front-end libraries with ready to use components for popular frameworks like React, Vue, Flutter, etc.

## Offerings

- **Amplify Studio**: Visually build a full-stack app (front-end Ul and backend)
- **Amplify CLI**: Configure an Amplify backend with a guided CLI workflow
- **Amplify Libraries**: Connect your app to existing AWS services
- **Amplify Hosting**: Host web apps via the AWS **CDN**

## Auth

- Provides authentication out of the box (`amplify add auth`)
- Can be integrated with Cognito.
- Supports MFA, Social Sign-in (federated identities)

## GraphQL API & Data Store
- Provides **GraphQL API using AppSync** and DynamoDB as the data store (`amplify add api`)
- Work with data locally with automatic synchronization to the cloud
- Visual data modeling using Amplify Studio

## Hosting

- Host web applications (`amplify add hosting`)
- Support for CICD, PR Reviews, Custom Domains, Monitoring, etc.
- Competing with services like **Netlify** and **Vercel**.


## Testing

- **Run unit tests during the build phase** (defined as test step in `amplify.yml`)
- Run **end-to-end tests during the test phase** 
    - Integrated with **Cypress** E2E testing framework