---
title: "github+hugo覚書"
date: 2021-12-27T17:34:36+09:00
draft: true
---

# 作業メモ



## config.toml

[``languageCode``はRSS用らしい](https://github.com/gohugoio/hugo/issues/349)。

```
languageCode = "ja"
```
## 参考

* [quickstart](https://gohugo.io/getting-started/quick-start/)
  * ローカルでの動作確認URL: [http://localhost:1313/](http://localhost:1313/)
  * WSL2だとLiveReloadが動かない？ -> symbolic link越しで/dev以下にリンクしていると動かないらしい。
* [githubにサイトをセットアップする](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
* [初期セットアップ](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-first-deployment-with-github_token)
* [step-by-step tutorial](https://levelup.gitconnected.com/build-a-personal-website-with-github-pages-and-hugo-6c68592204c7)
* [https://azleslie.com/projects/hugo-tutorial/](https://azleslie.com/projects/hugo-tutorial/)
