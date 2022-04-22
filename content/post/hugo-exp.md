---
title: "Hugo心得"
date: 2022-04-22T13:14:49+08:00
draft: true
tags: ["hugo", "github"]
categories: ["general"]
---

# 安裝hugo

# 創建一個hugo site
```
hugo new site hugo-web
```
# 套用主題

```
cd hugo-web

// 先把hugo-web納入git，等等會再指定他的origin到github的repo
git init

// 透過submodule的功能，將theme的repo導入指定的主題路徑
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/papermod

// 在config.toml裡註明要使用的theme
echo theme = \"papermod\" >> config.toml
```
# 在本機上運行hugo server
```
hugo server -D
```
* 這裡遇到一個問題，當在chrome裡拜訪 [http://localhost:1313/](http://localhost:1313/) 時，會遇到ERR_SSL_PROTOCOL_ERROR錯誤，chrome老是會去訪問https，後來發現只要把cache清掉即可正常訪問
* -D的意思是顯示目前draft=true的文章
# 把hugo-web上傳到github
```
cd hugo-web
git add .
git commit -m "My first commit for hugo blog"
git remote add origin https://github.com/chiakie/hugo-web.git
```



