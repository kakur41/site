---
title = 'Hugoのページをgithubで公開する'
date = 2023-10-20T00:28:25+09:00
draft = true
categories:
  - Web開発
tags:
  - Hugo
  - 静的サイトジェネレータ
  - github
  - git
---
# GitHub Actionsとトークンの設定について

## GitHub Actionsの仕組み
GitHub Actionsは、リポジトリ内の特定のイベント（例: プッシュやプルリクエスト）に対して自動化されたタスクを実行する仕組みです。YAMLファイルでワークフローを定義し、そのワークフローがGitHubのランナー上で実行されます。

## トークンの設定
GitHub Actionsでリポジトリへのアクセスを許可するためには、トークンの設定が必要です。以下の手順で設定します。

1. GitHubの[設定ページ](https://github.com/settings/tokens)にアクセスし、新しいトークンを生成します。
2. トークンのスコープを設定し、`repo`と`workflow`を選択します。
3. 生成したトークンをコピーし、リポジトリの`Settings` > `Secrets`に移動して新しいシークレットを作成します。
4. シークレットの名前を`gh_token`とし、値にトークンをペーストします。

これで、GitHub Actionsワークフロー内で`${{ secrets.gh_token }}`としてトークンを使用できます。
# HugoのページをGitHubで公開する
本題です。\
Hugoで作成された静的ウェブサイトをGitHub Pagesで公開する方法を解説します。

## ステップ1: Hugoプロジェクトの準備

- Hugoプロジェクトをローカルマシンで作成またはクローンします([参考](https://kakur41.github.io/site/%E3%81%9D%E3%81%AE%E4%BB%96%E6%8A%80%E8%A1%93/hugo%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9/))。
- hugo.tomlに下記を設定してください。
```
baseURL = 'https://{username}.github.io/{repository}'
```
- 作成したプロジェクトをGitHubリポジトリにプッシュします。

## ステップ2: GitHub Actionsの設定

- リポジトリのルートに`.github/workflows`ディレクトリを作成します。
- 以下の内容で`gh-pages.yml`ファイルを作成し保存します。
- themeに合わせて内容やバージョンを変更してください。
```yaml
name: github pages

on:
  push:
    branches:
      - master 
      
permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true 
          fetch-depth: 0 

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "0.119.0"

      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded

      - name: Build
        run: hugo

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.gh_token }}
          publish_dir: ./public
```

## ステップ3: GitHub Pagesの設定
デフォルトで設定されているはずですが下記を確認してください。
- リポジトリの`Settings`タブに移動します。
- 左側のメニューから`Pages`を選択します。
- `Source`セクションで、`Branch`ドロップダウンから`gh-pages`ブランチを選択し、`Save`ボタンをクリックします。

これで、Hugoで作成されたウェブサイトはGitHub Pagesで公開されるようになります。公開されたウェブサイトは`https://{username}.github.io/{repository}` のURLでアクセスできます。