# Trufflehog Action

A [trufflehog](https://github.com/trufflesecurity/truffleHog) action that looks
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

      - uses: ASFHyP3/actions/trufflehog@master
```
