# Faculty Development Team GitHub Actions

Useful actions shared between our repositories.

## bundle-update

Run `bundle update` against a repository and create a pull request with any changes.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `container` _(optional)_: Run in a container with ruby already initialised. 
* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
name: Bundle Update

on:
  schedule:
    - cron: '0 7 * * THU'
  workflow_dispatch:

jobs:
  bundle-update:
    name: Run bundle update
    runs-on: [ self-hosted, linux, x64 ]
    container: docker://ghcr.io/university-of-york/faculty-dev-docker-images/ci/aws-lambda-ruby-dev:2.7
    steps:
      - uses: university-of-york/faculty-dev-actions/bundle-update@v1
        with:
          checkout-key: ${{ secrets.BUNDLE_UPDATE_SSH_PRIVATE_KEY }}
          container: "true"
```

## bundle-update-dev

Run `bundle update --group development test` against a repository and _auto-merge_ the pull request with any changes.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `container` _(optional)_: Run in a container with ruby already initialised.
* `github-token`: the token used in the workflow to allow the PR to be updated and auto-merged.
* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
name: Bundle Update [development, test]

on:
  schedule:
    - cron: '0 6 * * *'
  workflow_dispatch:

permissions:
  pull-requests: write
  contents: write

jobs:
  bundle-update:
    name: Run `bundle update --group development test` and auto-merge
    runs-on: [ self-hosted, linux, x64 ]
    container: docker://ghcr.io/university-of-york/faculty-dev-docker-images/ci/aws-lambda-ruby-dev:2.7
    steps:
      - uses: university-of-york/faculty-dev-actions/bundle-update-dev-container@v1
        with:
          checkout-key: ${{ secrets.BUNDLE_UPDATE_SSH_PRIVATE_KEY }}
          container: "true"
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## bundler-audit

Run `bundle exec bundler-audit check --update` against a repository.

### Inputs

* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
jobs:
  bundler-audit:
    name: Bundler Audit
    runs-on: ubuntu-latest
    steps:
      - uses: university-of-york/faculty-dev-actions/bundler-audit@v1
```

## gemfury-deploy

Deploys the named gem to gemfury

### Inputs

* `gem-name`: the name of the gem to build
* `gemfury-push-token`: the token used to authenticate with gemfury
* `prerelease-only` _(optional)_: set to anything other than "false" to only upload prerelease versions
* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
jobs:
  gemfury-deploy:
    name: Gemfury Deployment
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    steps:
      - uses: university-of-york/faculty-dev-actions/gemfury-deployment@v1
        with:
          gem-name: uoy-faculty-new_gem
          gemfury-push-token: ${{ secrets.GEMFURY_PUSH_TOKEN }}
```

## npm-update

Runs `npm update` on a runner against a repository and create a pull request with any changes.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `node-version` _(optional)_: the version of node to use. Defaults to 14.
* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

## rspec-lambda

Run `rspec` tests in the AWS lambda environment.

### Inputs

* `artifact-name` _(optional)_: the name of the vue artifact to download.

### Example

This is an example with a postgres database available for tests.

```yaml
jobs:
  rspec:
    name: RSpec tests
    runs-on: [ self-hosted, linux, x64 ]
    container: docker://ghcr.io/university-of-york/faculty-dev-docker-images/ci/aws-lambda-ruby-dev:2.7
    services:
      postgres:
        image: ghcr.io/university-of-york/faculty-dev-db-pristine:latest
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: university-of-york/faculty-dev-actions/rspec-lambda@v1
        env:
          DB_HOST: postgres
          DB_USER: sinatra_base_app
```

## rspec-runner

Run `rspec` tests on a runner.

### Inputs

* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

## rubocop

Run `rubocop` against a repository.

### Inputs

* `working-directory` _(optional)_: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
jobs:
  rubocop:
    name: Rubocop
    runs-on: ubuntu-latest
    steps:
      - uses: university-of-york/faculty-dev-actions/rubocop@v1
```
