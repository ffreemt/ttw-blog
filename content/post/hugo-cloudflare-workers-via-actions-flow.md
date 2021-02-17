---
title: How to deploy a Hugo site to cloudflare workers site via Actions Workflow
date: 2021-02-17T17:48:26+08:00
draft: false
---


## For myself

To set ``name = "blog"`` and ``bucket = "public"`` in ``wrangler.toml``:
```bash
wrangler init --site blog
sed -i 's/bucket = ""/bucket = "public"/g' wrangler.toml

`deploy.yml` in ``.github/workflows`
```bash
name: Deploy to CF Workers

on:
  push:
    branches:
      - 'content/*'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Install Hugo
        run: sudo snap install hugo

      - name: Install Wrangler
        run: sudo npm i @cloudflare/wrangler -g

      - name: Build hugo minify gc
        run: hugo --minify --gc

      - name: Creatte wrangler.toml with name=blog and bucket=public
        env:
          CF_API_TOKEN: ${{ secrets.CF_API_TOKEN }}
        run: |
          echo $CF_API_TOKEN
          wrangler init --site blog
          sed -i 's/bucket = ""/bucket = "public"/g' wrangler.toml
          sed -i 's/account_id = ""/account_id = "'$CF_API_TOKEN'"/g' wrangler.toml
          echo wrangler.toml

      - name: config wrangler # run: CF_API_TOKEN=${{ secrets.CF_API_TOKEN }} wrangler publish
        run: |
          wrangler -h
          wrangler publish
```

# [https://developers.cloudflare.com/workers/cli-wrangler/configuration](https://developers.cloudflare.com/workers/cli-wrangler/configuration)
```

References

*   [https://github.com/cloudflare/wrangler-action](https://github.com/cloudflare/wrangler-action)
*   [https://blog.samrhea.com/posts/2019/workers-github-deploy](https://blog.samrhea.com/posts/2019/workers-github-deploy)

https://blog.samrhea.com/posts/2019/workers-github-deploy