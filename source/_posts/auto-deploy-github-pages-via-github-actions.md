---
title: (Reprint) Auto Deploy Github Pages via Github Actions
date: 2020-07-20 22:05:22
tags: Git
categories: Programming
---

{% note info%}
转载自：[通過 travis-ci 或者 GitHub Actions 自動化部署 GitHub Pages 和 Coding Pages](https://jerryc.me/posts/74006f42/#Travis-CI)
{% endnote %}

# GitHub Actions

`GitHub Actions` 是 GitHub 於 2019 年底推出的自動化流程工具，它的功能很強大。而我們只需要用到它的構建。由於是 GitHub 自家的工具，我們無需再額外的註冊賬號，GitHub Actions 也集成在了 GitHub 界面上，我們只需點擊 `Actions` 進行創建。

## 創建 Actions
在 Hexo 目錄的倉庫，點擊 `Actions Tab`, 然後點擊 `Set up a workflow yourself`
