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
* [`reusable-labeled-pr-check.yml`](.github/workflows/reusable-labeled-pr-check.yml) reusable workflow to ensure a Pull
  Request has been appropriately labeled for a release
* [`reusable-release.yml`](.github/workflows/reusable-release.yml) reusable workflow to create a release from a
  `CHANGELOG.md` file and synchronize the release and development branches
* [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml) reusable workflow to scan a PR for
  potentially committed secrets using [git-leaks](https://github.com/zricethezav/gitleaks)
  and [truffleHog](https://github.com/trufflesecurity/truffleHog)

### Deprecated
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