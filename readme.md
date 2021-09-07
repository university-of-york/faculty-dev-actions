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

## bundle-update-runner

As above, but expects to be run on a runner, not in a container, so will call `setup-ruby`.

### Inputs

* `checkout-key`: the SSH key to use to check out the repository.
  Details of setting up this key can be found in [the wiki](https://wiki.york.ac.uk/display/ittechdocs/Faculty+Dev%3A+New+Github+Repository).
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
