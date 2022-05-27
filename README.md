# Auto merge dependabot PRs without PAT after all workflows passed

CI checker action has been extracted to [kachick/wait-other-jobs](https://github.com/kachick/wait-other-jobs)
So this repository's source code is just history archive now.

## Why?

I used a way to comment "@dependabot merge". This is simple to ensure CI passed. However it requires PAT(Personal Access Token). It is all.
So this action provides another way. It checks other builds and merging the dependabot PR with GITHUB_TOKEN not PAT.

### Usage - new

[As this](https://github.com/kachick/ruby_library_template/blob/22b655d9d12cf0d34e54353eb467b2cdeb71b4e1/.github/workflows/auto-merge-dependabot-prs.yml)

## Usage - Old

```yaml
name: Auto merge dependabot PRs
on: pull_request

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge-dependabot-prs:
    runs-on: ubuntu-latest
    if: ${{ github.actor == 'dependabot[bot]' }}
     timeout-minutes: 30
    steps:
      - name: Dependabot metadata
        id: metadata
        uses: dependabot/fetch-metadata@v1.3.1
        with:
          github-token: "${{ secrets.GITHUB_TOKEN }}"
      - name: Auto merge Dependabot PRs
        if: ${{steps.metadata.outputs.update-type == 'version-update:semver-patch'}}
        uses: kachick/auto-merge-dependabot-prs-without-pat-after-workflows-passed@v1-beta
        with:
          github-token: "${{ secrets.GITHUB_TOKEN }}"
        env:
          GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}" # Currently needed to pass same value separately with ~ github-token
```

You can adjust status polling interval and merging strategy as below.

```yaml
        with:
          github-token: "${{ secrets.GITHUB_TOKEN }}"
          min-interval-seconds: 300 # default 30
          merging-strategy: "merge" # default "squash"
```

