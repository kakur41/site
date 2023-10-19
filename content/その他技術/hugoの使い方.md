---
title: "Hugoの使い方"
date: 2023-10-19T20:13:17+09:00
categories:
  - Web開発
tags:
  - Hugo
  - 静的サイトジェネレータ
---

## Introduction

Hugoは、高速で柔軟な静的サイトジェネレーターです。Go言語で書かれており、シンプルなコマンド一つでウェブサイトをビルドすることができます。

## Key Features of Hugo

- **高速**: Hugoは非常に高速で、大規模なサイトでも短時間でビルドすることができます。
- **柔軟性**: Hugoはテーマとカスタムテンプレートをサポートしており、ウェブサイトのデザインを簡単にカスタマイズできます。
- **マークダウンサポート**: Hugoはマークダウンファイルをサポートしており、コンテンツ作成が簡単で直感的です。

## Getting Started with Hugo

Hugoを使ってウェブサイトを作成するには、以下の手順を実行します:

1. Hugoのインストール:
    ```bash
    wget https://github.com/gohugoio/hugo/releases/download/v0.119.0/hugo_0.119.0_linux-amd64.deb
    # # https://github.com/gohugoio/hugo/releases/tag/v0.119.0 から選択 
    dpkg -i hugo_0.119.0_linux-amd64.deb
    ```

2. 新しいサイトの作成:
    ```bash
    hugo new site kakur41.github.io
    cd kakur41.github.io
    ```

3. テーマの追加:
    ```bash
    git submodule add -f https://github.com/JingWangTW/dark-theme-editor.git themes/dark-theme-editor
    echo 'theme = "dark-theme-editor"' >> config.toml
    ```

4. 新しいポストの作成:
    ```bash
    hugo new その他技術/hugoの使い方.md
    ```

5. サイトのビルドとプレビュー:
    ```bash
    hugo server -D
    ```

これで、`http://localhost:1313`にアクセスすることで、新しいHugoウェブサイトをプレビューできます。

## Conclusion

Hugoは、静的ウェブサイトの作成において強力かつ柔軟なツールです。高速なビルドタイムと広範なカスタマイズオプションは、ウェブ開発者にとって大きな利点です。
