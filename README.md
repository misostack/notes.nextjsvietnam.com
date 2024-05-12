# Getting start

> This website is built on vuepress@2.0.0-beta.60

## How to create a new vuepress website

### Quick start

```bash
npm init
npm install -D vuepress@next
```

Create your .gitignore file

```
node_modules
.temp
.cache
```

Add scripts to package.json

```json
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

Create your first document

```bash
mkdir docs
echo '# JsBaseVietNam' > docs/README.md
```

### Setup github actions and add your custom domain

Create github action yml

```bash
mkdir -p .github/workflows
touch .github/workflows/docs.yml
```

```yml
# .github/workflows/docs.yml
name: docs

on:
  # trigger deployment on every push to main branch
  push:
    branches: [main]
  # trigger deployment manually
  workflow_dispatch:

jobs:
  docs:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
        with:
          # fetch all commits to get last updated time or other git log info
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          # choose node.js version to use
          node-version: 18
          # cache deps for npm
          cache: npm

      - name: Install deps
        run: npm install

      # run build script
      - name: Build VuePress site
        run: npm run docs:build

      # please check out the docs of the workflow for more details
      # @see https://github.com/crazy-max/ghaction-github-pages
      - name: Deploy to GitHub Pages
        uses: crazy-max/ghaction-github-pages@v2
        with:
          # deploy to gh-pages branch
          target_branch: gh-pages
          # deploy the default output dir of VuePress
          build_dir: docs/.vuepress/dist
        env:
          # @see https://docs.github.com/en/actions/reference/authentication-in-a-workflow#about-the-github_token-secret
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Custom Domain: cheatsheet.jsbasevietnam.com

### Theme configuration

```bash
mkdir -p docs/.vuepress/public
echo "cheatsheet.jsbasevietnam.com" > docs/.vuepress/public/CNAME
```

```bash
touch docs/.vuepress/config.js
```

```js
module.exports = {
  title: "All in one cheatsheet made by JSBaseVietNam",
  description:
    "The confluence of all topics about programming, deployment, system design, copy and paste solutions, popular frameworks",
};
```