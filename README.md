# GitHub Action: bbot

This action runs `bbot` to scan a URL with the given parameters. Recommended use is with a @vX tag to ensure a stable version is used instead of @master. Versions can be found here [tags](https://github.com/thereisnotime/Action-bbot/tags) and bbot documentation for all parameters [here](https://github.com/blacklanternsecurity/bbot/wiki#installation).

NOTE: Due to weird versioning of the author the version is locked to 2.5.0 for now.

## Examples

### Start a scan (no module)

```yaml
- name: Scan with bbot
  uses: thereisnotime/action-bbot@master
  with:
    target: "blackfox-security.com"
```

### Weekly scheduled scans (enumerate subdomains)

```yaml
on:
  schedule:
    - cron: 0 11 * * 1 # Monday at 11 UTC

jobs:
  scan_bbot:
    runs-on: ubuntu-latest
    name: SCAN | bbot OSINT
    steps:
      - name: Scan with bbot
        uses: thereisnotime/action-bbot@master
        with:
          target: "blackfox-security.com"
          additional_args: "--yes -f subdomain-enum"
```

### Use latest version in a job

```yaml
scan_bbot:
  runs-on: ubuntu-latest
  name: SCAN | bbot OSINT
  steps:
    - name: Scan with bbot
      uses: thereisnotime/action-bbot@master
      with:
        target: "blackfox-security.com"
        additional_args: "--yes -f subdomain-enum"
```

### Use latest version in a job and upload the results back to the repository

```yaml
scan_bbot:
  runs-on: ubuntu-latest
  name: SCAN | bbot OSINT
  steps:
    - name: Prepare environment
      run: |
        echo "START_DATE=$(date +'%d-%m-%YT%H-%M-%S-%Z')" >> $GITHUB_ENV
        echo "START_TIMESTAMP=$(date +'%s')" >> $GITHUB_ENV
        TMP_TARGET="blackfox-security.com"
        echo "TARGET=$TMP_TARGET" >> $GITHUB_ENV
    - name: Checkout
      uses: actions/checkout@v3
    - name: Scan with bbot
      uses: thereisnotime/action-bbot@master
      with:
        target: ${{ env.TARGET }}
        additional_args: "--yes -f subdomain-enum"
    - name: Commit and push changes
      uses: EndBug/add-and-commit@v9
      with:
        author_name: GitHub Actions
        author_email: 41898282+github-actions[bot]@users.noreply.github.com
        message: "👷 chore(bbot): add report for ${{ env.TARGET }}-${{ env.START_DATE }}-${{ env.START_TIMESTAMP }}"
        add: "./reports/web/*"
        pull: "--rebase --autostash"
```

## Inputs

### `target`

**Required**. Target (e.g. blackfox-security.com)

### `additional_args`

Additional arguments to pass to bbot.

## Outputs

### `result`

JSON scan results.

### `resultb64`

JSON scan results, base64 encoded.

## Roadmap

If there is enough time or interest, I will add the following features (feel free to open PRs):

- [ ] Finish support for outputs (e.g. `result` and `resultb64`).
- [ ] Add support for notification via Slack.
- [ ] Add support for notification via Telegram.
- [ ] Add support for notification via email.
- [ ] Add support for notification via custom webhooks.
- [ ] Improve argument handling.
