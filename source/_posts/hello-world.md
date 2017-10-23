---
title: Hello World to Hexo
date: 2016-07-20 19:51:43
tags: hexo
---
[Hexo](https://hexo.io/) 是在 [StaticGen](https://www.staticgen.com/)
上排名第三的 static site generator. 可以幫你快速的建出 blog 等等的靜態網站.

## 設定方式

以下是在 hexo 3.2.2 和 arch linux 4.6.4 下的設定說明. 

<!--more-->

首先用 `npm` 在 global 安裝 `hexo`. 
```bash
npm install -g hexo
```

接著在你想要的地方開一個新的資料夾存放 blog 的檔案.
然後初始化 hexo.
```bash
mkdir [your directory]
cd $_
hexo init
```

最後在 `./config.yml` 裡面更改設定, 找出 Deployment 一區的選項,
這裡假設你要用 Github 當做 deploy 的位置.  
`[your username]` 填入你的 github 帳號.
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/[your username]/[your username].github.io
```

最後 generate 和 deploy 就會在 `[your username].github.io` 上看到你的網頁了!
```
hexo generate
hexo deploy
```
