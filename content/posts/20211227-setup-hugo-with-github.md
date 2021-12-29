---
title: "github + hugo"
date: 2021-12-27T17:34:36+09:00
categories: misc
tags: [hugo, github, blog]
---

## 作業メモ

[作業レポジトリ](https://github.com/jimbi-o/minimal-cpp-pj)

1. hugoをインストール
    * ``sudo apt install hugo``
1. [quickstart](https://gohugo.io/getting-started/quick-start/)に則ってコンテンツを作成
1. config.tomlのlanguageCodeを変更
    * ``languageCode = "ja"``
	* [RSS用の設定](https://github.com/gohugoio/hugo/issues/349)らしい
1. ルートディレクトリ下に.github/workflows/gh-pages.ymlを作成
    * [こちら](https://gohugo.io/hosting-and-deployment/hosting-on-github/)に中身が置いてある
1. githubでレポジトリ[username].github.ioを作成
1. [こちら](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-first-deployment-with-github_token)に従ってデプロイ用のトークンを設定
1. gitにcommitしてpushする
1. [username].github.ioのレポジトリのSettings > Pages > Github Pages > Source で Branch:mainになっているところをBranch:gh-pagesに変更
    * hugoのgithub actionsだとデフォルトでgh-pagesにコミットされるため

## 参考

* [quickstart](https://gohugo.io/getting-started/quick-start/)
  * ローカルでの動作確認URL: [http://localhost:1313/](http://localhost:1313/)
  * WSL2だとLiveReloadが動かない？ -> symbolic link越しで/dev/mnt以下にリンクしていると動かないらしい。
* [githubにサイトをセットアップする](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
* [初期セットアップ](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-first-deployment-with-github_token)
* [step-by-step tutorial](https://levelup.gitconnected.com/build-a-personal-website-with-github-pages-and-hugo-6c68592204c7)
* [another tutorial](https://azleslie.com/projects/hugo-tutorial/)
