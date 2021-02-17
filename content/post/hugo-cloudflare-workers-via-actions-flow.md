---
title: 如何将Hugo静态网页部署到Cloudflare的workers
date: 2021-02-17T17:48:26+08:00
draft: false
---

## 步骤
*   设置好cloudflare的workers。
*   Fork这个库
*   设置库的Secrets：
    * Name: `CF_API_TOKEN`
    * Value: `~/.wrangler/config/default.toml`或`%userprofile%\.wrangler\conf
ig\default.toml`里的 `api_token`或从Cloudflare上取。
*   改``config.toml``文件里的东西及`content`目录的文件。
*   `git add . && git commit -m "update" && git push`

## For self

To set ``name = "blog"`` in ``wrangler.toml``:
```bash
wrangler init --site blog
# add `account_id` and bucket = "public"
```

`deploy.yml` in ``.github/workflows`
```bash
name: Deploy to CF Workers

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    name: Deploy
    steps:
      - uses: actions/checkout@v2
      - name: Install Hugo
        run: sudo snap install hugo

      - name: Build hugo minify gc
        run: hugo --minify --gc

      - name: Publish
        uses: cloudflare/wrangler-action@1.3.0
        with:
          apiToken: ${{ secrets.CF_API_TOKEN }}
          preCommands: echo "*** pre command ***"
          postCommands: |
            echo "*** post commands ***"
            wrangler publish
            echo "******"

```

For record
```bash

      - name: Creatte wrangler.toml with name=blog and bucket=public
        run: |
          wrangler init --site blog
          sed -i 's/bucket = ""/bucket = "public"/g' wrangler.toml
          sed -i 's/account_id = ""/account_id = "'$CF_API_TOKEN'"/g' wrangler.toml
          echo wrangler.toml

      - name: config wrangler # run: CF_API_TOKEN=${{ secrets.CF_API_TOKEN }} wrangler publish
```


References

*   [https://github.com/cloudflare/wrangler-action](https://github.com/cloudflare/wrangler-action)
*   [https://developers.cloudflare.com/workers/cli-wrangler/configuration](https://developers.cloudflare.com/workers/cli-wrangler/configuration)

*   [https://blog.samrhea.com/posts/2019/workers-github-deploy](https://blog.samrhea.com/posts/2019/workers-github-deploy)

*   https://blog.samrhea.com/posts/2019/workers-github-deploy