# ASFHyP3 Actions

A collection of [GitHub Actions](https://docs.github.com/en/actions) and [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows)
that are used across the ASFHyP3 organization. 

**We reccomend pinning versions as these workflow sare designed to facilitate ASF Tools Team development and
may change as the team evolves.** 

## Reusable Workflows

A collection of [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows),
consolidating the duplicated workflows across the ASFHyP3 organization. 

### [`reusable-bump-version.yml`](./.github/workflows/reusable-bump-version.yml)

Creates a new version tag for a repository based on Pull Request labels using bump2version. Use like:

```yaml
name: Tag New Version

on:
  push:
    branches:
      - main

jobs:
  call-bump-version-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-bump-version.yml@v0.4.0
    with:
      user: tools-bot                # Optional; default shown
      email: UAF-asf-apd@alaska.edu  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```

To tag a new version on any merge to `main`. This workflow uses the optional 'user' and 'email' inputs, and the required
[personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
`USER_TOKEN` to define the user who will be creating and pushing the version tag.

### [`reusable-changelog-check.yml`](./.github/workflows/reusable-changelog-check.yml)

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
    uses: ASFHyP3/actions/.github/workflows/reusable-changelog-check.yml@v0.4.0
    secrets:
      USER_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

to ensure the changelog has been updated for any PR to `develop` or `main`. 

### [`reusable-docker-ecr.yml`](./.github/workflows/reusable-docker-ecr.yml)

Builds a Docker image from the `Dockerfile` in the repository root and pushes it to the
[Amazon Elastic Container Registry](https://aws.amazon.com/ecr/)
with the specified version tag, and is best paired with either the `reusable-version-info.yml` workflow or the `reusable-git-object-name.yml` workflow. This workflow will
additionally push the image with a `latest` and `test` tag for merges to the release and develop branch, respectively.
Use like:

```yaml
name: Build

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
  call-version-info-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.4.0
    with:
      conda_env_name: hyp3-plugin

  call-docker-ecr-workflow:
    needs: call-version-info-workflow
    uses: ASFHyP3/actions/.github/workflows/reusable-docker-ecr.yml@v0.4.0
    with:
      version_tag: ${{ needs.call-version-info-workflow.outputs.version_tag }}
      ecr_registry: 845172464411.dkr.ecr.us-west-2.amazonaws.com
      aws_region: us-west-2    # Optional; default shown
      release_branch: main     # Optional; default shown
      develop_branch: develop  # Optional; default shown
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

### [`reusable-docker-ghcr.yml`](./.github/workflows/reusable-docker-ghcr.yml)

Builds a Docker image from the `Dockerfile` in the repository root and pushes it to the 
[GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
with the specified version tag, and is best paired with the `reusable-version-info.yml` workflow or the `reusable-git-object-name.yml` workflow. This workflow will
additionally push the image with a `latest` and `test` tag for merges to the release and develop branch, respectively.
Use like:

```yaml
name: Build

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
  call-version-info-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.4.0
    with:
      conda_env_name: hyp3-plugin

  call-docker-ghcr-workflow:
    needs: call-version-info-workflow
    uses: ASFHyP3/actions/.github/workflows/reusable-docker-ghcr.yml@v0.4.0
    with:
      version_tag: ${{ needs.call-version-info-workflow.outputs.version_tag }}
      release_branch: main     # Optional; default shown
      develop_branch: develop  # Optional; default shown
      user: tools-bot          # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```

### [`reusable-git-object-name.yml`](./.github/workflows/reusable-git-object-name.yml)

Outputs the human-readable git object name from [`git describe`](https://git-scm.com/docs/git-describe) 
of the calling repository. Use like:

```yaml
name: Build

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
  call-git-object-name-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-git-object-name.yml@v0.4.0
  
  echo-git-object-name-outputs:
    needs: call-git-object-name-workflow
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "name: ${{ needs.call-git-object-name-workflow.outputs.name }}"
```
and is intended to be paired with workflows like the `reusable-docker-ghcr.yml` workflow.

### [`reusable-flake8.yml`](./.github/workflows/reusable-flake8.yml)

Runs [flake8](https://flake8.pycqa.org/en/latest/) to enforce ASFHyP3's Python style guide. Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-flake8-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-flake8.yml@v0.4.0
    with:
      local_package_names: hyp3_plugin  # Required; comma-seperated list of names that should be considered local to your application
      excludes: hyp3_plugin/ugly.py     # Optional; comma-separated list of glob patterns to exclude from checks
```

to ensure the Python code is styled correctly.

### [`reusable-labeled-pr-check.yml`](./.github/workflows/reusable-labeled-pr-check.yml)

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
    uses: ASFHyP3/actions/.github/workflows/reusable-labeled-pr-check.yml@v0.4.0
```
to ensure a release label is included on any PR to `main`.

### [`reusable-pytest.yml`](./.github/workflows/reusable-pytest.yml)

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
    uses: ASFHyP3/actions/.github/workflows/reusable-pytest.yml@v0.4.0
    with:
      local_package_name: hyp3_plugin  # Required; package to produce a coverage report for
      conda_env_name: hyp3-plugin      # Required; conda environment name to activate
      # Optional; default shown
      python_versions: >-
        ["3.8", "3.9", "3.10"]
```

to test your Python package and produce a coverage report for. Importantly, `python_versions` *must* be a valid JSON string
containing a list of python version strings.

### [`reusable-release.yml`](./.github/workflows/reusable-release.yml)

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
    uses: ASFHyP3/actions/.github/workflows/reusable-release.yml@v0.4.0
    with:
      release_prefix: HyP3-CI
      release_branch: main      # Optional; default shown
      develop_branch: develop   # Optional; default shown
      sync_pr_label: tools-bot  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```
to create a release for every newly pushed version tag.

### [`reusable-relese-checklist-comment.yml`](.github/workflows/reusable-release-checklist-comment.yml) 

Add a comment to PRs when they are opened with a release checklist for developers and reviewers.

Use like:

```yaml
on:
  pull_request:
    types:
      - opened
    branches:
      - main
  
jobs:
  call-release-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-relese-checklist-comment.yml@v0.4.0
    with:
      # optional; example shown
      additional_developer_items: '- [ ] If the step function code has changed, have you drained the job queue before merging?'
      # optional; example shown
      additional_reviewer_items: '- [ ] Are there any risks associated with this release?'
    secrets:
      USER_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
to add a comment to PRs when they are opened to the `main` branch.


### [`reusable-secrets-analysis.yml`](./.github/workflows/reusable-secrets-analysis.yml)

Scan a PR for potentially committed secrets using [truffleHog](https://github.com/trufflesecurity/truffleHog).
Requires this file at the root of the calling repository:
  * [`.trufflehot.txt`](./.trufflehog.txt)

Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-secrets-analysis-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-secrets-analysis.yml@v0.4.0
```
to scan every push for secrets.

### [`reusable-version-info.yml`](./.github/workflows/reusable-version-info.yml)

Outputs the version number of the calling repositories Python package by running `python setup.py --version` from the
repository root. Requires an `environment.yml` file at the root of the calling repository specifying all the runtime
dependencies needed. This workflow will additionally output a Docker tag compatible version number. Use like:

```yaml
name: Build

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
  call-version-info-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.4.0
    with:
      conda_env_name: hyp3-plugin  # Required; conda environment name to activate
      python_version: '3.9'        # Optional; default shown
  
  echo-version-info-outputs:
    needs: call-version-info-workflow
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "version: ${{ needs.call-version-info-workflow.outputs.version }}"
          echo "version tag: ${{ needs.call-version-info-workflow.outputs.version_tag }}"
```
and is intended to be paired with workflows like the `reusable-docker-ghcr.yml` workflow.
