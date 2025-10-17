# PostHog source maps github action

Inject and upload JavaScript source maps to PostHog using the PostHog CLI.

**Important:** This action does not build your project. You are responsible for compiling your app and generating source maps (for example, by running `npm run build`). This action only does the following:

- Injects source map references into your built files
- Uploads the source maps to PostHog

See the PostHog documentation for end-to-end guidance: [Upload source maps](https://posthog.com/docs/error-tracking/upload-source-maps).

## Inputs

| **Name**    | **Required** | **Description**                                                                                                                               |
| ----------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `directory` | Yes          | Directory containing built assets (e.g., `dist`)                                                                                              |
| `env-id`    | Yes          | PostHog environment ID. See [environment settings](https://app.posthog.com/settings/environment#variables)                                    |
| `cli-token` | Yes          | PostHog CLI token with error tracking write scope. See [API key settings](https://app.posthog.com/settings/user-api-keys#variables)                                           |
| `project`   | No           | Project identifier. If not provided, the action tries to read the Git repository name. If it cannot determine a value, the action fails       |
| `version`   | No           | Release/version (e.g., commit SHA). If not provided, the action uses the current commit SHA. If it cannot determine a value, the action fails |
| `host`      | No           | PostHog host URL. If you use the US cloud, you don't need to set this. For the EU cloud, set `https://eu.posthog.com`                         |

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
      - name: Inject & upload source maps to PostHog
        uses: PostHog/upload-source-maps@v0.4.6
        with:
          directory: dist
          env-id: ${{ secrets.POSTHOG_ENV_ID }}
          cli-token: ${{ secrets.POSTHOG_CLI_TOKEN }}

          # If using the EU cloud, set the host explicitly
          # host: https://eu.posthog.com

          # If a Git repository is not accessible, set the project explicitly
          # project: my-awesome-project

          # If the current Git commit is not accessible, set the version explicitly
          # version: 1.2.3
```
