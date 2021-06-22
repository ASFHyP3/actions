# Trufflehog Action

A [trufflehog](https://github.com/trufflesecurity/truffleHog) action that looks
for secrets in the git commit history back to the last git tag

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
          python-version: 3.8

      - uses: ASFHyP3/actions/trufflehog@master
```

*Note: `fetch-depth: 0` is required to checkout git tags.* 
