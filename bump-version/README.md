# Bump Version

Create a new tag for a repository based on Pull Request labels using bump2version.

This requires you to have:
* checked out the repository with `fetch-depth: 0` so git tags will be available
* run the [associated PR query](../.github/queries/associated-pr.query.yml) and stored its results in `pr.json`
* run the [PR labels query](../.github/queries/associated-pr.query.yml) and stored its results in `latbels.json`
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
          
      - uses: ASFHyP3/actions/bump-version@master
        with:
          user: ${{ secrets.USER_NAME }}
          email: ${{ secrets.USER_EMAIL }}
```
