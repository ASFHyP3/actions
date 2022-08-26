# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [PEP 440](https://www.python.org/dev/peps/pep-0440/) 
and uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## [0.2.0](https://github.com/ASFHyP3/actions/compare/v0.1.0...v0.2.0)

### Added
* [`reusable-bump-version.yml`](.github/workflows/reusable-bump-version.yml) reusable workflow to create a new version
  tag for a repository based on Pull Request labels using bump2version
* [`reusable-changelog-check.yml`](.github/workflows/reusable-changelog-check.yml) reusable workflow to ensure the
  changelog has been updated
* [`reusable-docker-ecr.yml`](./.github/workflows/reusable-docker-ecr.yml) reusable workflow to build a Docker image
  from the `Dockerfile` in the repository root and pushes it to the [Amazon Elastic Container Registry](https://aws.amazon.com/ecr/)
* [`reusable-docker-ghcr.yml`](./.github/workflows/reusable-docker-ghcr.yml) reusable workflow to build a Docker image
  from the `Dockerfile` in the repository root and pushes it to the [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
* [`reusable-flake8.yml`](./.github/workflows/reusable-flake8.yml) eusable workflow to enforce ASFHyP3's Python
  style guide with [flake8](https://flake8.pycqa.org/en/latest/)
* [`reusable-labeled-pr-check.yml`](.github/workflows/reusable-labeled-pr-check.yml) reusable workflow to ensure a Pull
  Request has been appropriately labeled for a release
* [`reusable-pytest.yml`](./.github/workflows/reusable-pytest.yml) reusable workflow to runs [pytest](https://docs.pytest.org/en/6.2.x/)
  in the repository's conda environment
* [`reusable-release.yml`](.github/workflows/reusable-release.yml) reusable workflow to create a release from a
  `CHANGELOG.md` file and synchronize the release and development branches
* [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml) reusable workflow to scan a PR for
  potentially committed secrets using [git-leaks](https://github.com/zricethezav/gitleaks)
  and [truffleHog](https://github.com/trufflesecurity/truffleHog)
* [`reusable-version-info.yml`](./.github/workflows/reusable-version-info.yml) reusable workflow to outputs the
  repository's Python package version number

### Deprecated

These actions are being depreciated and will be removed in the next release:
* The [bump-version action ](bump-version/README.md) in favor of the 
  [`reusable-bump-version.yml`](.github/workflows/reusable-bump-version.yml) reusable workflow
* The [trufflehog action](trufflehog/README.md) in favor of the
  [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml) reusable workflow

## [0.1.0](https://github.com/ASFHyP3/actions/compare/v0.0.0...v0.1.0)

### Added
* A [trufflehog action](trufflehog/README.md) that looks for secrets in the git commit
  history back to the last tag
* A [bump-version action ](bump-version/README.md) that creates a new tag for a repository
  based on Pull Request labels using bump2version
