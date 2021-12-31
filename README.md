# ASFHyP3 Actions

A collection of [GitHub Actions](https://docs.github.com/en/actions) and [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows)
that are used across the ASFHyP3 organization. 

**While these can generally be used broadly, they are designed to facilitate the ASF Tools Team development workflows and
may change as the team evolved. Therefore, we recommend pinning versions for repos not owned by the Tools Team.** 

## Reusable Workflows

A collection of [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows),
consolidating the duplicated workflows across the ASFHyP3 organization. 

### `reusable-bump-version.yml`

Creates a new version tag for a repository based on Pull Request labels using bump2version. Use like:

```yaml
name: Tag New Version

on:
  push:
    branches:
      - main

jobs:
  call-bump-version-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-bump-version.yml@main
    with:
      user: tools-bot                # Optional; default shown
      email: UAF-asf-apd@alaska.edu  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```

To tag a new version on any merge to `main`. This workflow uses the optional 'user' and 'email' inputs, and the required
[personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
`USER_TOKEN` to define the user who will be creating and pushing the version tag.

### `reusable-changelog-check.yml`

Ensures the changelog has been updated. Use like:

```yaml
name: Changelog updated?

on:
  pull_request:
    types:
      - opened
      - labeled
      - unlabeled
      - synchronize
    branches:
      - main
      - develop

jobs:
  call-changelog-check-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-changelog-check.yml@main
    secrets:
      USER_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

to ensure the changelog has been updated for any PR to `develop` or `main`. 

### `reusable-flake8.yml`

Runs [flake8](https://flake8.pycqa.org/en/latest/) to enforce ASFHyP3's Python style guide. Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-flake8-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-flake8.yml@main
    with:
      local_package_namkes: hyp3_plugin  # Required; comma-seperated list of names that should be considered local to your application
      excludes: hyp3_plugin/ugly.py      # Optional; comma-separated list of glob patterns to exclude from checks
```

to ensure the Python code is styled correctly.

### `reusable-labeled-pr-check.yml`

Ensures a PR has been appropriately labeled for a release. Use like:

```yaml
name: Is PR labeled?

on:
  pull_request:
    types:
      - opened
      - labeled
      - unlabeled
      - synchronize
    branches:
      - main

jobs:
  call-labeled-pr-check-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-labeled-pr-check.yml@main
```
to ensure a release label is included on any PR to `main`.

### `reusable-pytest.yml`

Runs [pytest](https://docs.pytest.org/en/6.2.x/). Requires an `environment.yml` file at the root of the calling
repository specifying all the runtime and testing dependencies needed. Use like:

```yaml
name: Test

on:
  push:
    branches:
      - main
      - develop
  pull_request:
    branches:
      - main
      - develop

jobs:
  call-pytest-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-pytest.yml@reusable-plugins
    with:
      local_package_name: hyp3_plugin  # Required; package to produce a coverage report for
      conda_env_name: hyp3-plugin      # Required; conda environment name to activate
      python_version: '3.9'            # Optional; default shown
```

to test your Python package and produce a coverage report for.

### `reusable-release.yml`

Creates a release from a `CHANGELOG.md` file and synchronize the release and development branches by:
* attempting to fast-forward merge the release branch into the develop branch
* falling back to opening a PR to bring the release branch into the develop branch. 

Use like:

```yaml
name: Create Release

on:
  push:
    tags:
      - 'v*'

jobs:
  call-release-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-release.yml@main
    with:
      release_prefix: HyP3-CI
      release_branch: main      # Optional; default shown
      develop_branch: develop   # Optional; default shown
      sync_pr_label: tools-bot  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```
to create a release for every newly pushed version tag.

### `reusable-secrets-analysis.yml`

Scan a PR for potentially committed secrets using [git-leaks](https://github.com/zricethezav/gitleaks)
and [truffleHog](https://github.com/trufflesecurity/truffleHog). Requires these files at the root of the calling repository:
  * [`.gitleaks.toml`](./.gitleaks.toml)
  * [`.trufflehot.txt`](./.trufflehog.txt)

Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-secrets-analysis-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-secrets-analysis.yml@main
```
to scan every push for secrets.

## Actions (Depreciated)

**WARNING! These actions have been depreciated in favor of their [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows)
equivalents.**

A collection of [composite run](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action) 
GitHub Actions to support ASFHyP3 CI/CD pipelines

* [TruffleHog](trufflehog/README.md) -- look for secrets in the git commit
  history back to the last tag
* [Bump Version](bump-version/README.md) -- create a new tag for a repository
  based on Pull Request labels using bump2version
