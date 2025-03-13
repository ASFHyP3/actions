# ASFHyP3 Actions

A collection of [GitHub Actions](https://docs.github.com/en/actions) and [reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows)
that are used across the ASFHyP3 organization. 

While these workflows are developed to be used broadly, we recommend strict version pinning when using these workflows.
They are primarily designed to facilitate ASF Tools Team development and will change as the team evolves.

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
    # For first-time setup, create a v0.0.0 tag as shown here:
    # https://github.com/ASFHyP3/actions#reusable-bump-versionyml
    uses: ASFHyP3/actions/.github/workflows/reusable-bump-version.yml@v0.17.1
    with:
      user: tools-bot                # Optional; default shown
      email: UAF-asf-apd@alaska.edu  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```

To tag a new version on any merge to `main`. This workflow uses the optional 'user' and 'email' inputs, and the required
[personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
`USER_TOKEN` to define the user who will be creating and pushing the version tag.

For this workflow to run successfully, there must be an annotated tag for the current version number.
When adding this workflow to a new repo, you should create a `v0.0.0` tag by running the following commands,
replacing `<commit>` with the hash of the initial commit:

```
git tag -am 'Marking zeroth release for auto-versioning and CI/CD Tooling' v0.0.0 <commit>
git push --tags
```

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
    uses: ASFHyP3/actions/.github/workflows/reusable-changelog-check.yml@v0.17.1
```

to ensure the changelog has been updated for any PR to `develop` or `main`. 

### [`reusable-create-jira-issue.yml`](./.github/workflows/reusable-create-jira-issue.yml)

Creates a Jira issue that corresponds to the labeled GitHub issue. Use like:

```yaml
name: Create Jira issue

on:
  issues:
    types: [labeled]

jobs:
  call-create-jira-issue-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-create-jira-issue.yml@v0.17.1
    secrets:
      JIRA_BASE_URL: ${{ secrets.JIRA_BASE_URL }}
      JIRA_USER_EMAIL: ${{ secrets.JIRA_USER_EMAIL }}
      JIRA_API_TOKEN: ${{ secrets.JIRA_API_TOKEN }}
      JIRA_PROJECT: ${{ secrets.JIRA_PROJECT }}
      JIRA_FIELDS: ${{ secrets.JIRA_FIELDS }}
```

The `JIRA_FIELDS` secret stores additional fields in JSON format. For example, to assign the issue to a particular
sprint, supply the following value:

```json
{"customfield_XXXXX": 42}
```

where `customfield_XXXXX` is the custom field name of the sprint field and `42` is the ID of the particular sprint.

It would seem that the custom field name of the sprint field is not the same across all Jira deployments and projects.
Therefore, to determine both the custom field name and the sprint ID, do the following:

1. Choose any issue that belongs to the particular sprint and run the `curl` command for the Jira API's
   [Get issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-get)
   endpoint, supplying the issue key.
2. Search the JSON response for the name of the sprint. You should find something like the following
   (there will be other fields such as `boardId` present in the object, but they are not shown below):
   ```json
   "customfield_XXXXX":[{"id":42,"name":"MySprint"}]
   ```

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
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.17.1
    with:
      conda_env_name: hyp3-plugin

  call-docker-ecr-workflow:
    needs: call-version-info-workflow
    uses: ASFHyP3/actions/.github/workflows/reusable-docker-ecr.yml@v0.17.1
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
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.17.1
    with:
      conda_env_name: hyp3-plugin

  call-docker-ghcr-workflow:
    needs: call-version-info-workflow
    uses: ASFHyP3/actions/.github/workflows/reusable-docker-ghcr.yml@v0.17.1
    with:
      version_tag: ${{ needs.call-version-info-workflow.outputs.version_tag }}
      user: ${{ github.actor }}
      release_branch: main     # Optional; default shown
      develop_branch: develop  # Optional; default shown
      file: Dockerfile # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### [`reusable-ruff.yml`](./.github/workflows/reusable-ruff.yml)

Runs [ruff](https://docs.astral.sh/ruff/) to enforce a configurable Python style guide. Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-ruff-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-ruff.yml@v0.17.1
```

Runs inside a mamba environment and assumes the presence of an `environment.yml` file.
We recommend pinning to an exact version of `ruff` in your project dependencies,
because upgrading `ruff` may result in new static analysis errors being reported.

Make sure that `pyproject.toml` contains the appropriate Python version specifier
(see the [ruff docs](https://docs.astral.sh/ruff/settings/#target-version)), e.g:

```toml
[project]
requires-python = ">=3.13"
```

To conform to ASFHyP3's Python style add the following to `pyproject.toml`
(and update the `src =` line as needed, for import ordering):

<!-- When updating these options, please also update
     https://github.com/ASFHyP3/hyp3-cookiecutter/blob/develop/%7B%7Bcookiecutter.__project_name%7D%7D/pyproject.toml -->
```toml
[tool.ruff]
line-length = 120
# The directories to consider when resolving first- vs. third-party imports.
# See: https://docs.astral.sh/ruff/settings/#src
src = ["src", "tests"]

[tool.ruff.format]
indent-style = "space"
quote-style = "single"

[tool.ruff.lint]
extend-select = [
    "I",   # isort: https://docs.astral.sh/ruff/rules/#isort-i
    "UP",  # pyupgrade: https://docs.astral.sh/ruff/rules/#pyupgrade-up
    "D",   # pydocstyle: https://docs.astral.sh/ruff/rules/#pydocstyle-d
    "ANN", # annotations: https://docs.astral.sh/ruff/rules/#flake8-annotations-ann
    "PTH", # use-pathlib-pth: https://docs.astral.sh/ruff/rules/#flake8-use-pathlib-pth
]

[tool.ruff.lint.pydocstyle]
convention = "google"

[tool.ruff.lint.isort]
case-sensitive = true
lines-after-imports = 2

[tool.ruff.lint.flake8-annotations]
suppress-dummy-args = true

[tool.ruff.lint.extend-per-file-ignores]
"*" = [
    "D1", # Ignore warnings about missing docstrings
    "ANN401", # Ignore warnings about use of the Any type
]
"tests/*" = ["ANN"]
```

Notes about the [flake8-annotations (ANN)](https://docs.astral.sh/ruff/rules/#flake8-annotations-ann) extension:

- Checks for missing type annotations, but does not perform static type checking (that's mypy's job).

- Mypy also includes options for enforcing type annotations
  (see [`disallow_untyped_defs`](https://mypy.readthedocs.io/en/stable/config_file.html#confval-disallow_untyped_defs)
  and [`disallow_incomplete_defs`](https://mypy.readthedocs.io/en/stable/config_file.html#confval-disallow_incomplete_defs)).
  We started using ruff's `ANN` extension before mypy, so we are sticking with `ANN` for enforcing type annotations for now.

- Feel free to remove this extension if you do not want to use static typing for your project.
  Alternatively, you may want to enable the
  [`ignore-fully-untyped`](https://docs.astral.sh/ruff/settings/#lint_flake8-annotations_ignore-fully-untyped) option
  (similar to mypy's [`disallow_incomplete_defs`](https://mypy.readthedocs.io/en/stable/config_file.html#confval-disallow_incomplete_defs) option)
  when adding the `ANN` extension to an existing codebase with portions of untyped code.

### [`reusable-mypy.yml`](./.github/workflows/reusable-mypy.yml)

> [!WARNING]
> Mypy does not turn Python into a statically-typed language,
> especially when mixing typed and untyped code, using the `Any` type annotation,
> or importing untyped third-party libraries.
> Type annotations are more like hints about what types values *should* have,
> rather than a guarantee of runtime type safety.
> They are not a replacement for best practices when using a dynamically-typed language.
> For more information about how mypy accommodates dynamic typing,
> see the docs on [dynamic typing](https://mypy.readthedocs.io/en/stable/dynamic_typing.html)
> and the [`Any` type](https://mypy.readthedocs.io/en/stable/kinds_of_types.html#the-any-type).

Runs [mypy](https://mypy-lang.org/) to perform static type checking for Python code. Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-mypy-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-mypy.yml@v0.17.1
```

Runs inside a mamba environment and assumes the presence of an `environment.yml` file.
We recommend pinning to an exact version of `mypy` in your project dependencies,
because upgrading `mypy` may result in new type errors being reported.

To use our recommended configuration options, add the following to `pyproject.toml`:

<!-- When updating these options, please also update
     https://github.com/ASFHyP3/hyp3-cookiecutter/blob/develop/%7B%7Bcookiecutter.__project_name%7D%7D/pyproject.toml -->
```toml
[tool.mypy]
python_version = ""
warn_redundant_casts = true
warn_unused_ignores = true
warn_unreachable = true
strict_equality = true
check_untyped_defs = true
install_types = true
non_interactive = true
pretty = true
disable_error_code = ["import-untyped"]
```

Populate the
[`python_version`](https://mypy.readthedocs.io/en/stable/config_file.html#confval-python_version) field
with the appropriate Python version in the format `MAJOR.MINOR`.
We recommend setting this to the minimum Python version supported by your project,
determined by the value of the
[`requires-python`](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/#python-requires) field
in your `pyproject.toml` file.

If you do not want mypy to analyze the bodies of functions without type annotations
(as described [here](https://mypy.readthedocs.io/en/stable/getting_started.html#getting-started-dynamic-vs-static)),
you should remove the [`check_untyped_defs`](https://mypy.readthedocs.io/en/stable/command_line.html#cmdoption-mypy-check-untyped-defs) option.
This can be helpful when adding mypy to an existing codebase with portions of untyped code,
or if you simply want the ability to opt out of static type checking on a per-function basis.

You may need to enable additional options depending on the needs of your project.
See our [Mypy](https://github.com/ASFHyP3/.github-private/wiki/Mypy) wiki article for configuration tips.

### [`reusable-git-object-name.yml`](./.github/workflows/reusable-git-object-name.yml)

Outputs the human-readable git object name from [`git describe --dirty --tags --long --match "*[0-9]*"`](https://git-scm.com/docs/git-describe)
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
    uses: ASFHyP3/actions/.github/workflows/reusable-git-object-name.yml@v0.17.1
  
  echo-git-object-name-outputs:
    needs: call-git-object-name-workflow
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "name: ${{ needs.call-git-object-name-workflow.outputs.name }}"
```
This workflow is intended to be paired with workflows like the `reusable-docker-ghcr.yml` workflow.

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
    uses: ASFHyP3/actions/.github/workflows/reusable-labeled-pr-check.yml@v0.17.1
```
to ensure a release label is included on any PR to `main`.

### [`reusable-pytest.yml`](./.github/workflows/reusable-pytest.yml)

Runs [pytest](https://docs.pytest.org/en/6.2.x/) and [pytest-cov](https://pypi.org/project/pytest-cov/). Requires an `environment.yml` file at the root of the calling
repository specifying all the runtime and testing dependencies needed, including `pytest` and `pytest-cov`. Use like:

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
    uses: ASFHyP3/actions/.github/workflows/reusable-pytest.yml@v0.17.1
    with:
      local_package_name: hyp3_plugin  # Required; package to produce a coverage report for
      fail_fast: false      # Optional; default shown
      python_versions: >-  # Optional; default shown
        ["3.9", "3.10", "3.11", "3.12"]
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
    uses: ASFHyP3/actions/.github/workflows/reusable-release.yml@v0.17.1
    with:
      release_prefix: HyP3-CI
      release_branch: main      # Optional; default shown
      develop_branch: develop   # Optional; default shown
      sync_pr_label: tools-bot  # Optional; default shown
    secrets:
      USER_TOKEN: ${{ secrets.TOOLS_BOT_PAK }}
```
to create a release for every newly pushed version tag.

### [`reusable-release-checklist-comment.yml`](.github/workflows/reusable-release-checklist-comment.yml) 

Add a comment to PRs when they are opened with a release checklist for developers and reviewers.

Use like:

```yaml
name: Create Release Comment

on:
  pull_request:
    types:
      - opened
    branches:
      - main
  
jobs:
  call-release-checklist-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-release-checklist-comment.yml@v0.17.1
    permissions:
      pull-requests: write
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

> [!IMPORTANT]
> This action assumes your stable release branch is named `main`. 

Use like:

```yaml
name: Static analysis

on: push

jobs:
  call-secrets-analysis-workflow:
    uses: ASFHyP3/actions/.github/workflows/reusable-secrets-analysis.yml@v0.17.1
```
to scan every push for secrets.

### [`reusable-version-info.yml`](./.github/workflows/reusable-version-info.yml)

Outputs the version number of the calling repositories Python package by running
[`python -m setuptools_scm`](https://github.com/pypa/setuptools_scm#pyprojecttoml-usage) from the repository root.
Requires an `environment.yml` file at the root of the calling repository specifying all the runtime
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
    uses: ASFHyP3/actions/.github/workflows/reusable-version-info.yml@v0.17.1
    with:
      python_version: '3.12'        # Optional; default shown

  echo-version-info-outputs:
    needs: call-version-info-workflow
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "version: ${{ needs.call-version-info-workflow.outputs.version }}"
          echo "version tag: ${{ needs.call-version-info-workflow.outputs.version_tag }}"
```
This workflow is intended to be paired with workflows like the `reusable-docker-ghcr.yml` workflow.
