# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [PEP 440](https://www.python.org/dev/peps/pep-0440/) 
and uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## [0.8.3]

### Added 
- `reusable-pytest` now tests Python 3.9--3.12 by default.

## [0.8.2]

### Changed
* `reusable-pytest` and `reusable-version-info` now use [`setup-micromamba`](https://github.com/mamba-org/setup-micromamba) rather than `provision-with-micromamba` which has been deprecated.

## [0.8.1]

### Added
* Modified [`reusable-create-jira-issue.yml`](./.github/workflows/reusable-create-jira-issue.yml) to add
  a disclaimer for the Jira issue link.

## [0.8.0]

### Added
* Added [`reusable-create-jira-issue.yml`](./.github/workflows/reusable-create-jira-issue.yml) for creating a Jira
  issue that corresponds to the labeled GitHub issue.
* [`reusable-pytest.yml`](.github/workflows/reusable-pytest.yml) now includes a `fail_fast` option which lets you specify 
  [the strategy for handling failures](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategyfail-fast)

## [0.7.1]

### Fixed
* [`reusable-docker-ghcr.yml`](.github/workflows/reusable-docker-ghcr.yml) no longer attempts
  to push a Docker image if run from a fork.

## [0.7.0]

### Changed
* `conda` environments are now provisioned with micromamba in all reusable workflows
* [`reusable-pytest.yml`](.github/workflows/reusable-pytest.yml) and [`reusable-pytest.yml`](.github/workflows/reusable-version-info.yml)
  no longer accept a `conda_env_name` input and will use the environment named in the calling repo's `environment.yml`

## [0.6.0]

### Changed
* [`reusable-version-info.yml`](.github/workflows/reusable-version-info.yml) now uses `setuptools_scm`for generating
  version numbers as the use of `setup.py` [is discouraged](https://setuptools.pypa.io/en/latest/userguide/quickstart.html#setuppy-discouraged)
* [`reusable-git-object-name.yml`](.github/workflows/reusable-git-object-name.yml) now handles lightweight as well as
  annotated tags

### Fixed
* All uses of the GitHub Action `set-output` command have been upgraded, due to
  forthcoming [depreciation](https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands/)

## [0.5.0]

### Added
* [`reusable-relese-checklist-comment.yml`](.github/workflows/reusable-release-checklist-comment.yml) reusable workflow to
  add a comment to PRs with a release checklist for developers and reviewers

### Changed
* [`reusable-pytest.yml`](.github/workflows/reusable-pytest.yml) now accepts (only) a JSON list of python version strings
  and will setup a matrix of test jobs for multiple python versions. `python_version` usage has changed from
  ```yaml
    python_version: "3.9"            # Optional; default shown  
  ```
  to 
  ```yaml
      # Optional; default shown
      python_versions: >-
        ["3.8", "3.9", "3.10"]
  ```
  Note: A single python version can be specified like `["3.9"]`.

### Removed
* Removed support for secret scanning with `gitleaks-action`, which now requires a paid license to use the latest version.

## [0.4.0](https://github.com/ASFHyP3/actions/compare/v0.3.0...v0.4.0)

### Added
* [`reusable-git-object-name.yml`](./.github/workflows/reusable-git-object-name.yml) workflow to
  output the human-readable git object name

## [0.3.0](https://github.com/ASFHyP3/actions/compare/v0.2.0...v0.3.0)

### Fixed
* [`reusable-docker-ecr.yml`](./.github/workflows/reusable-docker-ecr.yml) workflow now ensures
  docker image names are all lower case

### Removed
* Depreciated truffleHog action; use the [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml)
  workflow instead
* Depreciated bump-version action; use the [`reusable-bump-version.yml`](.github/workflows/reusable-bump-version.yml)
  workflow instead

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
