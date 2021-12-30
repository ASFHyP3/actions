# Bump Version

**WARNING! This action has been depreciated in favor of the [`reusable-bump-version.yml`](../.github/workflows/reusable-bump-version.yml)
[reusable workflow](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).**
See the [README](../README.md) for instructions on how to use the new workflow.

Create a new tag for a repository based on Pull Request labels using bump2version.

This requires you to have:
* checked out the repository with `fetch-depth: 0` so git tags will be available
* run the [associated PR query](../.github/queries/associated-pr.query.yml) and stored its results in `pr.json`
* run the [PR labels query](../.github/queries/associated-pr.query.yml) and stored its results in `labels.json`
* setup python

## Usage

```yaml
  tag-version:
    runs-on: ubuntu-latest
    needs: pytest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
          token: ${{ secrets.USER_TOKEN }}

      - name: Get associated PR
        uses: helaili/github-graphql-action@2.0.1
        env:
          GITHUB_TOKEN:  ${{ secrets.USER_TOKEN }}
        with:
          query: .github/queries/associated-pr.query.yml
          outputFile: pr.json
          owner: ${{ github.repository_owner }}
          name: {{ github.event.repository.name }}
          sha: ${{ github.sha }}

      - name: Get PR labels
        uses: helaili/github-graphql-action@2.0.1
        env:
          GITHUB_TOKEN: ${{ secrets.USER_TOKEN }}
        with:
          query: .github/queries/pr-labels.query.yml
          outputFile: labels.json
          owner: ${{ github.repository_owner }}
          name: {{ github.event.repository.name }}

      - uses: actions/setup-python@v1
        with:
          python-version: 3.x
          
      - uses: ASFHyP3/actions/bump-version@v0.2.0
        with:
          user: ${{ secrets.USER_NAME }}
          email: ${{ secrets.USER_EMAIL }}
```
