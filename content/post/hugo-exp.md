---
title: "Hugo心得"
date: 2022-04-22T13:14:49+08:00
draft: true
tags: ["hugo", "github", "git"]
categories: ["general"]
---

# 安裝hugo
* [https://github.com/gohugoio/hugo/releases](https://github.com/gohugoio/hugo/releases) 下載對應作業系統提供的最新版
* 視主題的情況可能要下載hugo_extended的版本
* 以Windows為例，將下載下來的zip解壓縮到C:\hugo，再把路徑(C:\hugo)加到環境變數的Path底下

# 創建一個hugo site
```
$ hugo new site hugo-web
```
# 套用主題

```
$ cd hugo-web

// 先把hugo-web納入git，等等會再指定他的origin到github的repo
$ git init

// 透過submodule的功能，將theme的repo導入指定的主題路徑
$ git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/papermod

// 在config.toml裡註明要使用的theme
$ echo theme = \"papermod\" >> config.toml
```
# 在本機上運行hugo server
```
$ cd hugo-web
$ hugo server -D
```
* 這裡遇到一個問題，當在chrome裡拜訪 [http://localhost:1313/](http://localhost:1313/) 時，會遇到ERR_SSL_PROTOCOL_ERROR錯誤，chrome老是會去訪問https，後來發現只要把cache清掉即可正常訪問
* -D的意思是顯示目前draft=true的文章
# 把hugo-web上傳到github
```
$ cd hugo-web
$ git add .
$ git commit -m "My first commit for hugo blog"

// 新增遠端版本庫(origin)
$ git remote add origin https://github.com/chiakie/hugo-web.git

// 上傳master並追蹤遠端(origin)的master
$ git push -u origin master
```
* -u 等於 --set-upstream，第一次把branch上傳時會用到，之後就不需要指定
* 如果對git指令不熟，可以先把hugo-web.git先pull下來，再把hugo new site產生的內容丟進去，再做commit & push
# 新增文章
```
$ cd hugo-web
$ hugo new post/my-first-post.md
```
* 會在content/post/裡新增文章，內容如下：
```
---
title: "My First Post"
date: 2019-03-26T08:47:11+01:00
draft: true
tags: [""]
categories: [""]
---

<content with markdown syntax>
```
# 生成靜態網頁並與github page連結
```
$ cd hugo-web

// 透過submodule的功能，把github page的repo設定到指定路徑public/
$ git submodule add https://github.com/chiakie/chiakie.github.io.git public

// 生成靜態網頁 (-D會產生包含draft=true的文章)
$ hugo
$ hugo -D

// commit & push to github-pages repo
$ cd public/
$ git add .
$ git ci
$ git push
```
* 靜態頁面會產生進public/裡
* 等github-pages deployment結束後後，再去訪問[https://chiakie.github.io](https://chiakie.github.io) 即可看到剛剛新增的文章
