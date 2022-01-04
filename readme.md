# Faculty Development Team Github Actions

Useful actions shared between our repositories.

## bundle-update-container

Run `bundle update` _in a container_ against a repository and create a pull request with any changes.

This action assumes the container already has `bundler` installed.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).

### Example

This is for an AWS sinatra app. Other uses may not need the container (or to run on self-hosted runners). 

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
```

## bundle-update-dev-container

Run `bundle update --group test development` _in a container_ against a repository and _auto-merge_ the pull request with any changes.

This action assumes the container already has `bundler` installed.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `github-token`: the token used in the workflow to allow the PR to be updated and auto-merged

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
    name: Run bundle update --group test development and auto-merge
    runs-on: [ self-hosted, linux, x64 ]
    container: docker://ghcr.io/university-of-york/faculty-dev-docker-images/ci/aws-lambda-ruby-dev:2.7
    steps:
      - uses: university-of-york/faculty-dev-actions/bundle-update-dev-container@v1
        with:
          checkout-key: ${{ secrets.BUNDLE_UPDATE_SSH_PRIVATE_KEY }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## bundle-update-runner

As `bundle-update-container`, but expects to be run on a runner, not in a container, so will call `setup-ruby`.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

## bundle-update-dev-runner

As `bundle-update-dev-container`, but expects to be run on a runner, not in a container, so will call `setup-ruby`.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `github-token`: the token used in the workflow to allow the PR to be updated and auto-merged
* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

## bundler-audit

Run `bundle exec bundler-audit check --update` against a repository.

### Inputs

* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
jobs:
  bundler-audit:
    name: Bundler Audit
    runs-on: ubuntu-latest
    steps:
      - uses: university-of-york/faculty-dev-actions/bundler-audit@v1
```

## npm-update

Runs `npm update` on a runner against a repository and create a pull request with any changes.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
* `node-version`: the version of node to use. Defaults to 14.
* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

## rspec-lambda

Run `rspec` tests in the AWS lambda environment.

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

## rspec-lambda-vue

Run `rspec` tests in the AWS lambda environment, and download built Vue components from an artifact. 

### Inputs

* `artifact-name`: the name of the artifact to download. Defaults to `vue-components`.

## rspec-runner

Run `rspec` tests on a runner.

### Inputs

* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

## rubocop

Run `rubocop` against a repository.

### Inputs

* `working-directory`: the working directory where Gemfile can be found. Defaults to the repository root.

### Example

```yaml
jobs:
  rubocop:
    name: Rubocop
    runs-on: ubuntu-latest
    steps:
      - uses: university-of-york/faculty-dev-actions/rubocop@v1
```
