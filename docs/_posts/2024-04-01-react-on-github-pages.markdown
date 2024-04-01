---
layout: default
title: "React app on Github Pages"
---

# React app on Github Pages

This is simple instruction how to deploy your React app on Github Pages

## Install dependecies

```
# Install gh-pages
npm install --save gh-pages
```

## Add info about your domain in package.json

```
{
  "name": "some-repo",
  "version": "0.1.0",
  "homepage": "https://<GITHUB_NICK>.github.io/some-repo",
  ...
}
```

## Generate public/private key

```
ssh-keygen -t rsa -b 2048
```

## Add deploy key

Create in Github repo deploy key using generated public key

![](https://raw.githubusercontent.com//barto14753/blog/gh-pages/docs/assets/react-on-github-pages/add_deploy_key.png)

## Add secret

Create in Github repo secret using generated private key

![](https://raw.githubusercontent.com//barto14753/blog/gh-pages/docs/assets/react-on-github-pages/add_secret.png)

## Add Github action

Create `.github/workflows/deploy.yml` file with deployment definition

```
name: Deploy React Application

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build_test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.13.0]
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
      - name: npm ci, build and test
        working-directory: ./your-project
        run: |
          npm ci
          npm install
          npm run build --if-present
      - name: deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.YOUR_DEPLOY_KEY }}
          publish_dir: ./your-project/build

```

## Create `gh-pages` branch

Run `git checkout -b gh-pages` to create new branch dedicated for deployment/build files

## Setup Github Pages

Set `gh-pages` branch as a root

![](https://raw.githubusercontent.com//barto14753/blog/gh-pages/docs/assets/react-on-github-pages/setup-gh-pages.png)

## Push changes

Push changes to trigger deployment

```
git add .
git commit -m 'Add gh-pages deployment'
git push
```

## Chack deployment

Open `https://<GITHUB_NICK>.github.io/some-repo`
