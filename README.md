# Workflow to import GitHub repositories to Snyk
This GitHub Actions workflow automatically imports GitHub repositories to Snyk, a security platform that helps developers find and fix vulnerabilities in their code.

## Usage
To use this workflow, you need to add the following code to your repository's `.github/workflows/snyk-import.yml` file:

```yaml
on:
  schedule:
    - cron: '0 0 * * *'

jobs:
  snyk-import:
    runs-on: ubuntu-latest
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      SNYK_LOG_PATH: "."
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install dependencies
        uses: actions/setup-node@v2
        with:
          node-version: '14.x'
      - name: Install dependencies
        run: npm install
      - name: Install snyk-import 
        run: npm install snyk-api-import@latest -g
      - name: Create import data for snyk
        run: DEBUG=*snyk* snyk-api-import import:data --orgsData=${{ github.workspace }}/snyk-orgs.json --source=github
      - name: Import data to snyk
        run: DEBUG=*snyk* snyk-api-import import --file=${{ github.workspace }}/github-import-targets.json 

```

This workflow runs on a schedule using a cron expression, which you can customize to your needs. By default, the workflow runs every day at midnight UTC.


You also need to add two secrets to your repository : 

- `GITHUB_TOKEN`: a personal access token with repo and `read:org` scopes.
- `SNYK_TOKEN`: an API token for Snyk.

## Steps
The workflow performs the following steps:

- Use the `GITHUB_TOKEN` and `SNYK_TOKEN` secrets as environment variables.
- Check out the repository's code.
- Install `Node.js` and its dependencies.
- Install the `snyk-api-import` package globally.
- Create import data for Snyk, using the snyk-orgs.json file in the repository's root directory and the GitHub source.
- Import the data to Snyk, using the `github-import-targets.json` file in the repository's root directory.

## Conclusion
With this workflow, you can easily import your GitHub repositories to Snyk and start monitoring them for vulnerabilities. For more information on how to use Snyk, please refer to the official documentation.
