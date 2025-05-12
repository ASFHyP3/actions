# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [PEP 440](https://www.python.org/dev/peps/pep-0440/) 
and uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.19.0]

### Added
- Added an optional `runner` input for [`reusable-ruff`](.github/workflows/reusable-ruff.yml) and [`reusable-mypy`](.github/workflows/reusable-mypy.yml) to allow specifying a custom GitHub Actions runner. Helps to address https://github.com/ASFHyP3/hyp3-testing/issues/128

## [0.18.1]

### Added
- Added `permissions` field for every job in the README examples to address https://github.com/ASFHyP3/actions/issues/220

### Changed
- Changed the default value for the `reusable-release.yml` workflow's `sync_pr_label` input from `tools-bot` to `bumpless`.

### Fixed
- Removed unnecessary `actions/checkout` step from [`reusable-release-checklist-comment.yml`](.github/workflows/reusable-release-checklist-comment.yml) so that it does not require the `contents: read` permission for private repos.
- Replaced the `conda_env_name` input parameter with `python_version` for the `reusable-version-info` job in the docker build README examples.
- The use or requirements of GitHub issue/pull request labels in the reusable workflows are now described in the README. 

## [0.18.0]

### Added
- Added notes to our [ruff documentation](https://github.com/ASFHyP3/actions#reusable-ruffyml) on how to relax the rules for docstrings and type annotations.
- Added a disclaimer to our [mypy documentation](https://github.com/ASFHyP3/actions#reusable-mypyyml) regarding the limitations of using a static type checker on Python code.
- Added a note about how the [`check_untyped_defs`](https://mypy.readthedocs.io/en/stable/command_line.html#cmdoption-mypy-check-untyped-defs) option included in our recommended mypy settings differs from mypy's [default static vs. dynamic typing behavior](https://mypy.readthedocs.io/en/stable/getting_started.html#getting-started-dynamic-vs-static).

### Changed
- Updated our [recommended ruff settings](https://github.com/ASFHyP3/actions#reusable-ruffyml) to suppress type annotation warnings for dummy function parameters (e.g. `_`) and ignore *all* `D1` rules (missing docstring warnings) for `tests/`:

  ```diff
  +[tool.ruff.lint.flake8-annotations]
  +suppress-dummy-args = true
  +
   [tool.ruff.lint.extend-per-file-ignores]
  -"tests/*" = ["D100", "D103", "ANN"]
  +"tests/*" = ["D1", "ANN"]
  ```

### Fixed
- The link to our [Mypy wiki article](https://github.com/ASFHyP3/.github/wiki/Mypy) is now publicly accessible.

## [0.17.1]

### Fixed
- Fixed `ruff: command not found` bug in [`reusable-ruff`](.github/workflows/reusable-ruff.yml), introduced by the previous release.

## [0.17.0]

### Changed
- The [`reusable-ruff`](.github/workflows/reusable-ruff.yml) workflow now runs inside a mamba environment and assumes the presence of an `environment.yml` file.
- We now recommend pinning to an exact version for both `ruff` and `mypy` in your project dependencies, to avoid unexpected static analysis errors due to unpinned dependency upgrades.
- We now recommend adding the following lines to `pyproject.toml`, to bring our recommended `ruff` settings in line with [hyp3-cookiecutter](https://github.com/ASFHyP3/hyp3-cookiecutter/blob/v0.3.3/%7B%7Bcookiecutter.__project_name%7D%7D/pyproject.toml#L56-L84):

    ```toml
    [tool.ruff.lint.extend-per-file-ignores]
    "tests/*" = ["D100", "D103", "ANN"]
    ```

## [0.16.0]

### Changed
- The [`reusable-ruff`](.github/workflows/reusable-ruff.yml) workflow will now run the ruff format step whether or not the ruff check step failed.

## [0.15.0]

### Removed
- The [`reusable-mypy`](.github/workflows/reusable-mypy.yml) workflow no longer passes any optional arguments to the `mypy` command. This makes it easier to run `mypy` locally, because the command is just:

  ```
  mypy .
  ```

  If you want to preserve the removed options for your project, you must now add them to `[tool.mypy]` in `pyproject.toml`:

  ```toml
  install_types = true
  non_interactive = true
  pretty = true
  ignore_missing_imports = true
  ```

  Note that we discourage enabling `ignore_missing_imports` as a global option (see <https://github.com/ASFHyP3/actions/issues/225>). You can always find our currently recommended configuration options in the [README](https://github.com/ASFHyP3/actions#reusable-mypyyml).

## [0.14.0]

### Added
- [`reusable-mypy`](.github/workflows/reusable-mypy.yml) reusable workflow for performing static type checking with [mypy](https://github.com/python/mypy).
- [`reusable-ruff`](.github/workflows/reusable-ruff.yml) now prints the suggested commands for automatically fixing errors.

### Removed
- `reusable-flake8.yml` has been removed. We recommend [`reusable-ruff`](.github/workflows/reusable-ruff.yml) instead.

## [0.13.2]

### Fixed
- Fix a bug in which [`reusable-ruff`](.github/workflows/reusable-ruff.yml) errors did not cause the action to fail.

## [0.13.1]

### Changed
- Reverts the addition of `--select I` to [`reusable-ruff`](.github/workflows/reusable-ruff.yml), as this option is already included in the recommended `pyproject.toml` settings given in the README.

## [0.13.0]

### Changed
- The [`reusable-ruff`](.github/workflows/reusable-ruff.yml) action now checks for import order.

### Fixed
- The [`reusable-release-action`](.github/workflows/reusable-release.yml) now uses the `gh` CLI instead of the archived `repo-sync/pull-request` action.

## [0.12.0]
### Added
- Releases of ASFHyP3/actions will now trigger updates to the ASFHyP3/hyp3-cookiecutter
- Optional ability to specify the name of the dockerfile to build with the `reusable-docker-ghcr` action.

## [0.11.2]

### Changed
- The default Python version used for [`reusable-version-info.yml`](./.github/workflows/reusable-version-info.yml) has been upgraded from 3.9 to 3.12.

## [0.11.1]

### Fixed
- Modified the [`reusable-changelog-check`](.github/workflows/reusable-changelog-check.yml) workflow to remove its dependency on a third-party action. The workflow behavior should remain unchanged except that applying the `bumpless` label now results in the workflow being skipped rather than succeeding. Fixes https://github.com/ASFHyP3/actions/issues/156
  - When upgrading this reusable action in your repository, remove the following lines from the calling workflow:
    ```diff
    -    secrets:
    -      USER_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    ```

## [0.11.0]

### Fixed
- [`update-examples`](.github/workflows/update-examples.yml) workflow will now strip distance and hash (longest `-*` match) from the input or calculated version numbers.
- [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml) now uses the Trufflehog Github Action to scan for only verified secrets, which should reduce or eliminate false positives.

### Removed
- [`reusable-secrets-analysis.yml`](.github/workflows/reusable-secrets-analysis.yml) no longer recognizes `.trufflehog.txt`, which was previously used to specify exclude paths.

## [0.10.0]

### Added
- Added items to the release checklist comment to remind developer to verify changes in test deployment.

### Changed
- [`update-examples`](.github/workflows/update-examples.yml) workflow is now triggerable with a `workflow_dispatch` event

## [0.9.0]

### Added 
- [`reusable-ruff`](.github/workflows/reusable-ruff.yml) reusable workflow for perform linting and static analysis
  with [Ruff](https://github.com/astral-sh/ruff).
- [`update-examples`](.github/workflows/update-examples.yml) workflow to keep the pinned version number in the README
  examples up to date.

## [0.8.3]

### Added 
- `reusable-pytest` now tests Python 3.9--3.12 by default.

## [0.8.2]

### Changed
* `reusable-pytest` and `reusable-version-info` now use [`setup-micromamba`](https://github.com/mamba-org/setup-micromamba) rather than 
  `provision-with-micromamba` which has been deprecated.

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
