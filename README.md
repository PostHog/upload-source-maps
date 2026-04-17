# PostHog source maps github action

Inject and upload JavaScript source maps to PostHog using the PostHog CLI.

**Important:** This action does not build your project. You are responsible for compiling your app and generating source maps (for example, by running `npm run build`). This action only does the following:

- Injects source map references into your built files
- Uploads the source maps to PostHog

See the PostHog documentation for end-to-end guidance: [Upload source maps](https://posthog.com/docs/error-tracking/upload-source-maps).

## Requirements

This action installs `@posthog/cli` at the version specified by the `cli-version` input (defaults to `latest`). Individual inputs may require a minimum CLI version — see the [CLI CHANGELOG](https://github.com/PostHog/posthog/blob/master/cli/CHANGELOG.md) for feature history. If you pin `cli-version`, make sure it's recent enough to support the inputs you use.

## Inputs

| **Name**                | **Required** | **Description**                                                                                                                                      |
| ----------------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `directory`             | Yes          | Directory containing built assets (e.g., `dist`)                                                                                                     |
| `project-id`            | Yes          | PostHog project ID. See [project settings](https://app.posthog.com/settings/project#variables)                                                       |
| `api-key`               | Yes          | PostHog personal API key with error tracking write scope. See [API key settings](https://app.posthog.com/settings/user-api-keys#variables)           |
| `release-name`          | No           | Release name. If not provided, the action tries to read the Git repository name. If it cannot determine a value, the action fails                    |
| `release-version`       | No           | Release version (e.g., commit SHA). If not provided, the action uses the current commit SHA. If it cannot determine a value, the action fails        |
| `build`                 | No           | Build number (e.g., `42`, `CFBundleVersion` on iOS, `versionCode` on Android). Stored as release metadata.                                           |
| `host`                  | No           | PostHog host URL. If you use the US cloud, you don't need to set this. For the EU cloud, set `https://eu.posthog.com`                                |
| `delete-after-upload`   | No           | Whether to delete the source map files after uploading them (default: `false`)                                                                       |
| `skip-ssl-verification` | No           | Whether to skip SSL verification when uploading chunks - only use when using self-signed certificates for self-deployed instances (default: `false`) |
| `batch-size`            | No           | The maximum number of chunks to upload in a single batch (default: 50)                                                                               |
| `ignore`                | No           | One or more directory glob patterns to ignore (comma-separated, e.g., `node_modules,*.test.js`)                                                      |
| `cli-version`           | No           | PostHog CLI version to use (e.g., `0.5.29`). If not provided, the latest version is used                                                             |

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
        uses: PostHog/upload-source-maps@v2
        with:
          directory: dist
          project-id: ${{ secrets.POSTHOG_PROJECT_ID }}
          api-key: ${{ secrets.POSTHOG_CLI_API_KEY }}

          # If using the EU cloud, set the host explicitly
          # host: https://eu.posthog.com

          # If a Git repository is not accessible, set the release name explicitly
          # release-name: my-awesome-project

          # If the current Git commit is not accessible, set the release version explicitly
          # release-version: 1.2.3

          # Attach a build number as release metadata
          # build: 42

          # Delete source map files after uploading
          # delete-after-upload: true

          # Skip SSL verification (only for self-signed certificates)
          # skip-ssl-verification: true

          # Custom batch size for uploads
          # batch-size: 100

          # Ignore specific patterns
          # ignore: node_modules,*.test.js,coverage

          # Pin to a specific CLI version
          # cli-version: 0.5.7
```
