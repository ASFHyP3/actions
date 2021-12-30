# truffleHog Action

**WARNING! This action has been depreciated in favor of the [`reusable-secrets-analysis.yml`](../.github/workflows/reusable-secrets-analysis.yml)
[reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).**
See the [README](../README.md) for instructions on how to use the new workflow.

A [truffleHog](https://github.com/trufflesecurity/truffleHog) action that looks
for secrets in the git commit history back to the last git tag

This requires you to have:
* checked out the repository with `fetch-depth: 0` so git tags will be available 
* a [`.trufflhog.txt`](../.trufflehog.txt) at the root of your repository
* setup python

## Usage

```yaml
  trufflehog:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - uses: actions/setup-python@v1
        with:
          python-version: 3.9

      - uses: ASFHyP3/actions/trufflehog@v0.2.0
```
