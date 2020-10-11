---
title: (转载) 通过 Github Action 自动部署 Github pages
date: 2020-07-20 22:05:22
tags: Git
categories: Programming
copyright: false
---

{% note info%}
转载自：[通過 travis-ci 或者 GitHub Actions 自動化部署 GitHub Pages 和 Coding Pages](https://jerryc.me/posts/74006f42/#Travis-CI)
{% endnote %}

# GitHub Actions

`GitHub Actions` 是 GitHub 於 2019 年底推出的自動化流程工具，它的功能很強大。而我們只需要用到它的構建。由於是 GitHub 自家的工具，我們無需再額外的註冊賬號，GitHub Actions 也集成在了 GitHub 界面上，我們只需點擊 `Actions` 進行創建。

## 創建 Actions
在 Hexo 目錄的倉庫，點擊 `Actions Tab`, 然後點擊 `Set up a workflow yourself`

把以下代碼複製到代碼框去, 注意如果倉庫中使用到子模塊，則需要將`submodules`設置為`true`

```yaml
name: 自動部署 Hexo

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [10.x]

    steps:
      - name: 開始運行
        uses: actions/checkout@v1
        with:
          submodules: true

      - name: 設置 Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      - name: 安裝 Hexo CI
        run: |
          export TZ='Asia/Shanghai'
          npm install hexo-cli -g

      - name: 緩存
        uses: actions/cache@v1
        id: cache-dependencies
        with:
          path: node_modules
          key: ${{runner.OS}}-${{hashFiles('**/package-lock.json')}}

      - name: 安裝插件
        if: steps.cache-dependencies.outputs.cache-hit != 'true'
        run: |
          npm install

      - name: 部署博客
        run: |
          hexo clean && hexo g && hexo douban && gulp
          cd ./public
          git init
          git config user.name "${{secrets.GIT_NAME}}"
          git config user.email "${{secrets.GIT_EMAIL}}"
          git add .
          git commit -m "Update"
          git push --force --quiet "https://${{secrets.GH_TOKEN}}@${{secrets.GH_REF}}" master:master
```

- `on` 是要被監測的 `branch`, 等同 Travis-ci 的 `branches`
- `GIT_NAME`, `GIT_EMAIL`, `GH_TOKEN`, `GH_REF` 均是爲了保密需要，需要在 `secrets` 進行額外設置的全局變量

## 設置隱私資料

在 `Settings` 裏的 `Secrets` 裏設置全局變量

- `GIT_NAME`: 用戶名
- `GIT_EMAIL`: 郵件
- `GH_REF`: 倉庫地址，以`.git`結尾
- `GH_TOKEN`: 在`Setting`->`Developer Setting`->`Personal access tokens`->`Generate new token`中申請一個`public_repo`的`token`即可

## 運行
現在你只要把 Hexo 的目錄推送到 GitHub 去，GitHub Actions 會自動監測到變化，然後進行編譯。

# 參考

- [Github Actions 自動部署 Hexo 腳本](https://eallion.com/github-actions-hexo-ci/)
- [travis.demo by Molunerfinn](https://github.com/Molunerfinn/hexo-theme-melody/blob/dev/.travis.demo.yml)