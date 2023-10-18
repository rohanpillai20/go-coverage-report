# go-coverage-report

[![Tests](https://github.com/Madrapps/jacoco-report/actions/workflows/check.yml/badge.svg)](https://github.com/Madrapps/jacoco-report/actions/workflows/check.yml)

A Github action that publishes the Go Coverage report as a comment in the Pull Request with customizable pass percentage for
modified modules, files and the overall project. You can view the coverage of just the changed files in your pull request.

## Usage

### Pre-requisites

Create a workflow `.yml` file in your repositories `.github/workflows` directory.
An [example workflow](#example-workflow) is available below. For more information, reference the GitHub Help
Documentation
for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file).

### Inputs

- `paths` - [**required**] Comma separated paths of the generated jacoco xml files (supports wildcard glob pattern)
- `token` - [**required**] Github personal token to add commits to Pull Request
- `min-coverage-overall` - [*optional* {default: 80%}] The minimum code coverage that is required to pass for overall project
- `min-coverage-changed-files` - [*optional* {default: 80%}] The minimum code coverage that is required to pass for changed files
- `update-comment` - [*optional* {default: false}] If true, updates the previous coverage report comment instead of creating new one.
  Requires `title` to work properly
- `title` - [*optional*] Title for the Pull Request comment
- `skip-if-no-changes` - [*optional* {default: false}] If true, comment won't be added if there is no coverage information present for
  the files changed
- `pass-emoji` - [*optional* {default: :green_apple:}] Emoji to use for pass status shown when 'coverage >= min coverage' (should be a Github supported emoji).
- `fail-emoji` - [*optional* {default: :x:}] Emoji to use for fail status shown when 'coverage < min coverage' (should be a Github supported emoji).
- `continue-on-error` - [*optional* {default: true}] If true, then do not fail the action on error, but log a warning
- `debug-mode` - [*optional* {default: false}] If true, run the action in debug mode and get debug logs printed in console

### Outputs

- `coverage-overall` - The overall coverage of the project
- `coverage-changed-files` - The total coverage of all changed files

### Example Workflow

```yaml
name: Measure coverage

on:
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - name: Install Go
        uses: actions/setup-go@v3
        with:
          go-version: 1.20.x
      - name: Run unit tests
        run: go test tests/... -v -coverpkg=./... -coverprofile=coverage.out

      - name: Add coverage to PR
        id: jacoco
        uses: rohanpillai20/jacoco-report@v1.6.1
        with:
          paths: |
            ${{ github.workspace }}/**/build/reports/go/prodNormalDebugCoverage/prodNormalDebugCoverage.xml,
            ${{ github.workspace }}/**/build/reports/go/**/debugCoverage.xml
          token: ${{ secrets.GITHUB_TOKEN }}
          min-coverage-overall: 40
          min-coverage-changed-files: 60
```

<br>
<img src="/preview/single-module-screenshot.png" alt="single module screenshot" title="single module screenshot" width="700" />
<br>
<img src="/preview/multi-module-screenshot.png" alt="multi-module screenshot" title="multi-module screenshot" width="700" />

## Contributing

We welcome contributions, and if you're interested, have a look at the [CONTRIBUTING](CONTRIBUTING.md) document.

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
