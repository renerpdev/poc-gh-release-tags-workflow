# POC - Tags Release Workflow

This project is deployed on Vercel with three different environments: production, staging, and development.

## Environment Links

### Production
The production environment represents the live version of the application that is accessible to end-users. 

**URL:** [https://poc-gh-release-tags-workflow.vercel.app](https://poc-gh-release-tags-workflow.vercel.app)

### Staging
The staging environment is used for testing and quality assurance before deploying to production.

**URL:** [https://poc-gh-release-tags-workflow--staging.vercel.app](https://poc-gh-release-tags-workflow--staging.vercel.app)

### Development
The development environment is used for ongoing development and testing of new features.

**URL:** [https://poc-gh-release-tags-workflow--dev.vercel.app](https://poc-gh-release-tags-workflow--dev.vercel.app)

## About the Project

This project is intended to showcase how the Tags Release Workflow works. It leverages Vercel's powerful deployment and preview features to maintain separate environments for different stages of development.

## Deployment

This project uses Vercel for deployment. Each environment is automatically updated following this workflow:

- Production: on every **release** (Preview deployment when creating a draft)
- Staging: on every **pre-release** (Preview deployment when creating a draft)
- Development: on merge to `main` branch (Preview deployment on PR open)

## Getting Started

To get started with this project:

1. Clone the repository
2. Install dependencies with `npm install`
3. Edit */public/index.html*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
