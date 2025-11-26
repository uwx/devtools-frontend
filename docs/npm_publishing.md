# NPM Publishing Workflow

This repository includes a GitHub Actions workflow that automatically builds and publishes the DevTools frontend to npm as `@zas/devtools-frontend`.

## Workflow: `build-and-publish-npm.yml`

The workflow is triggered on:
- Push to the `main` branch (publishes a dev/prerelease version)
- Push of tags starting with `v*` (publishes a stable version)
- Manual workflow dispatch

## Required Secrets

To enable npm publishing, you need to configure the following secret in your GitHub repository:

### `NPM_TOKEN`

An npm authentication token with publish permissions for the `@zas/devtools-frontend` package.

**How to create and configure:**

1. Log in to npm (or create an account if you don't have one)
2. Create an organization named `zas` (or use an existing one you have access to)
3. Generate an access token:
   - Go to your npm profile settings
   - Click "Access Tokens"
   - Click "Generate New Token"
   - Choose "Automation" token type (required for CI/CD)
   - Copy the generated token
4. Add the token to your GitHub repository:
   - Go to your repository settings
   - Navigate to "Secrets and variables" → "Actions"
   - Click "New repository secret"
   - Name: `NPM_TOKEN`
   - Value: Paste your npm token
   - Click "Add secret"

## Publishing Behavior

### On Tag Push (Stable Release)
When you push a tag like `v1.0.0`:
- The workflow builds the project
- Sets the package version to match the tag (e.g., `1.0.0`)
- Publishes to npm with the `latest` tag
- Uses npm provenance for supply chain security

### On Main Branch Push (Development Release)
When you push to the `main` branch:
- The workflow builds the project
- Creates a prerelease version like `0.0.0-dev.20231126123456.abc1234`
- Publishes to npm with the `dev` tag
- Users can install with `npm install @zas/devtools-frontend@dev`

### Manual Trigger
You can also manually trigger the workflow from the Actions tab in GitHub.

## Build Process

The workflow:
1. Checks out Chromium's `depot_tools` for build dependencies
2. Syncs project dependencies using `gclient`
3. Generates build files using `gn`
4. Builds the project using `autoninja`
5. Publishes the built artifacts to npm

## Package Information

- **Package Name**: `@zas/devtools-frontend`
- **Scope**: `@zas`
- **Access**: Public (anyone can install)
- **License**: BSD-3-Clause

## Installing the Package

```bash
# Install the latest stable version
npm install @zas/devtools-frontend

# Install the latest development version
npm install @zas/devtools-frontend@dev
```

## Notes

- The workflow removes the internal `.npmrc` file before publishing to ensure it publishes to the public npm registry
- Build artifacts in the `out/` directory are excluded from the npm package (see `.npmignore`)
- The workflow uses npm provenance for enhanced security and transparency
