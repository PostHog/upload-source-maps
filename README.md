# PostHog source maps action

Inject and upload JavaScript sourcemaps to PostHog using the PostHog CLI.

**Important:** This action does not build your project. You are responsible for compiling your app and generating source maps (for example, by running `npm run build`). This action only:

- Injects source map references into your built files
- Uploads the source maps to PostHog

See the PostHog documentation for end-to-end guidance: [Upload source maps](https://posthog.com/docs/error-tracking/upload-source-maps).

## Inputs

| **Name**    | **Required** | **Description**                                                                                                                                                      |
| ----------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `directory` | Yes          | Directory with built assets (e.g., `dist`)                                                                                                                           |
| `env-id`    | Yes          | PostHog environment ID. See [environment settings](https://app.posthog.com/settings/environment#variables)                                                           |
| `cli-token` | Yes          | PostHog CLI token. See [api key settings](https://app.posthog.com/settings/user-api-keys#variables)                                                                  |
| `project`   | No           | Project identifier. Defaults to input from this flag. If not provided, it tries to read git repository name. If neither available, it throws an error                |
| `version`   | No           | Release/version (e.g., commit SHA). Defaults to input from this flag. If not provided, it tries to read current commit SHA. If neither available, it throws an error |
| `host`      | No           | PostHog host URL. If you're on the US PostHog host, you don't need to provide this. If you're on the EU host, provide `https://eu.posthog.com`.                      |

## Example usage

```yaml
name: My main workflow

on:
  push:
    branches: [main]

jobs:
  build_and_upload:
    runs-on: ubuntu-latest
    steps:
      # Build the application and generate source maps
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build

      # Inject and upload source maps using this action
      - name: Inject & upload sourcemaps to PostHog
        uses: ablaszkiewicz/posthog-upload-sourcemaps@v0.2
        with:
          directory: dist
          env-id: ${{ secrets.POSTHOG_ENV_ID }}
          cli-token: ${{ secrets.POSTHOG_CLI_TOKEN }}

          # If using the EU cloud, set the host explicitly
          # host: https://eu.posthog.com

          # If there is not git repository accessible, set the project explicitly
          # project: my-awesome-project

          # If there is not git commit accessible, set the version explicitly
          # version: 1.2.3
```
