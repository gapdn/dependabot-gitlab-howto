# dependabot-gitlab-howto
This guide describes how to set up *Dependabot* for Gitlab repositories using [`dependabot-gitlab`](https://gitlab.com/dependabot-gitlab/dependabot) in a `service mode`

### Step 1: Add configuration file
  - Add configuration file .gitlab/dependabot.yml to the project. Minimal configuration requires following options to be present:
```yaml
version: 2
updates:
  - package-ecosystem: bundler
    directory: /
    schedule:
      interval: daily

```
Some of the options have default values. Here is an example:
```yaml
version: 2
registries: 'default: none'
vulnerability-alerts: 'default: { enabled: true }'
updates:
  - package-ecosystem: 'required'
    directory: 'required'
    schedule:
      interval: 'required'
      day: 'default: "random day"' # for weekly updates
      time: 'default: "random time"'
      timezone: 'default: "server timezone"'
      hours: 'default: "00-24"' # for random time
    pull-request-branch-name:
      separator: 'default: "-"'
      prefix: 'default: "dependabot"'
      max-length: 40
    allow:
      - dependency-type: 'default: "direct"'
    open-pull-requests-limit: 'default: 5'
    open-security-pull-requests-limit: 'default: 10'
    versioning-strategy: 'default: "auto"'
    rebase-strategy: 'default: "auto"'
    auto-merge: 'default: false'
    fork: 'default: false'
    vendor: 'default: false'
    insecure-external-code-execution: 'default: false'
    registries: 'default: "*"'
    labels: 'default: none'
    commit-message: 'default: none'
    milestone: 'default: none'
    ignore: 'default: none'
    assignees: 'default: none'
    reviewers: 'default: none'
    approvers: 'default: none'
    updater-options: 'default: none'
    vulnerability-alerts: 'default: enabled: true, confidential: true'
    disable-mr-creation: 'default: false'

```
For full list of options, plese visit [GitHub documentation](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file)
### Step 2: Set up access tokens
- Create Gitlab personal access token with `api` access scope.[[Docs](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html)]
- Create github personal access token with `public_repo` access scope if all your dependencies come from public repositories or `repo` scope if some dependencies might need to fetch changelog data from private repositories.
Setting up github access token is optional but due to very low rate limit for anonymous users, it is highly recommended for updates to work properly.[[Docs](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)]
### Step 3: Start the app
- Clone the project
```bash
git clone https://gitlab.com/dependabot-gitlab/dependabot.git && cd dependabot
```
- Set up required environment variables
```bash
export SETTINGS__GITLAB_URL=https://gitlab.com
export SETTINGS__GITLAB_ACCESS_TOKEN=your_gitlab_access_token
export SETTINGS__GITHUB_ACCESS_TOKEN=your_github_access_token
```
- Start app using `docker-compose`
```bash
docker compose up -d
```
### Step 4: Add project
```bash
docker compose run --rm web bundle exec rake dependabot:automatic_registration
```
There are a bunch of rake tasks to configure projects
https://gitlab.com/dependabot-gitlab/dependabot/-/blob/main/lib/tasks/dependabot.rake

After running dependabot app you get UI to manage projects
![Dependabot UI](/images/ui.png?raw=true)
### Step 5: Deploy
- Helm chart

  Add helm repo:
  ```bash
  helm repo add dependabot https://dependabot-gitlab.gitlab.io/chart
  ```
  Install application:
  ```bash
  helm install dependabot dependabot/dependabot-gitlab --values values.yaml
  ```
- Docker compose
  ```bash
  docker compose up -d
  ```
