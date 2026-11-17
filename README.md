# Nais GitHub team sync deployment

Deploys [nais/github-teamsync](https://github.com/nais/github-teamsync) for
`navikt` in team `nais`, environment `prod-gcp`. This repository owns the
[application manifest](.nais/app.yaml) and
[deployment workflow](.github/workflows/deploy.yaml), not the application image.

## Setup

Create these resources in Nais Console:

- **Config `nais-github-teamsync`**: set the environment variables listed in the
  manifest, starting with `DRY_RUN=true`.
- **Secret `nais-github-teamsync-github-app`**: store the GitHub App's PEM private
  key as `private-key.pem`.

Authorize this repository to deploy for team `nais`. Bind the application to a
Nais API service account with access to the full team inventory and member emails
(after the first deployment if needed). Nais injects the rotating workload token;
do not store it in configuration or secrets.

## Deploy

Run **Deploy GitHub team sync** in GitHub Actions with a published upstream image
reference, preferably pinned by digest. The workflow previews the manifest, then
deploys and waits for readiness.

Review dry-run logs and identity mappings before setting `DRY_RUN=false` in Nais
Config: synchronization can remove memberships and delete managed teams.
The application runs as one replica with `Recreate` to avoid overlapping syncs.

See the [upstream documentation](https://github.com/nais/github-teamsync#readme)
for GitHub App permissions, identity providers, and synchronization behavior, and
the [Nais documentation](https://docs.nais.io/) for platform configuration.
