---
title: 'CTFdを触ってコンテストの作り方を知る'
date: 2023-10-22T17:50:32+09:00
draft: false
categories:
  - CTFd
tags:
  - CTFd
  - Docker
---

# ローカル環境でCTFdスコアサーバを立ち上げる

CTFdは、セキュリティのコンテストやトレーニングのためのプラットフォームを提供するオープンソースのフレームワークです。この記事では、ローカル環境でCTFdのスコアサーバを立ち上げ、実際に触ってみる方法を紹介します。
※公開まではやりません

## 前提条件

- Dockerがインストールされていること

## 手順1: CTFdのイメージをダウンロード

まずはじめに、Docker HubからCTFdの公式イメージをダウンロードします。

```
docker pull ctfd/ctfd
```

## 手順2: CTFdコンテナを立ち上げる

次に、以下のコマンドを実行してCTFdコンテナを立ち上げます。このコマンドは、CTFdサーバをバックグラウンドで実行し、8000番ポートを公開します。

```
docker run -d -p 8000:8000 ctfd/ctfd
```

## 手順3: CTFdにアクセスする

コンテナを立ち上げたら、ウェブブラウザを開いて `http://localhost:8000` にアクセスします。そうすると、CTFdのセットアップページが表示されます。

## 手順4: CTFdをセットアップする

セットアップページに従って、必要な情報を入力し、CTFdのインスタンスをセットアップします。これだけで準備完了です。

## 手順5: 問題を追加し、テストする

最後に、Adminとしてログインします。いくつかの問題をCTFdに追加し、ローカル環境での動作をテストします。問題を追加するには、Admin Panelタブにアクセスし、`Challenges` セクションから問題を追加できます。

![screen](/site/images/1.png)

# APIを利用してCTFdに問題を追加する

CTFdは、RESTful APIを提供しており、このAPIを利用することでプログラム的に問題を追加したり、他の操作を行うことができます。このセクションでは、Pythonの`requests`ライブラリを利用してCTFdのAPIを通じて問題を追加する方法を示します。

## 前提条件

- Python 3がインストールされていること
- `requests`ライブラリがインストールされていること

## 手順1: `requests`ライブラリをインストールする

まだインストールされていない場合は、以下のコマンドで`requests`ライブラリをインストールします。

```
pip install requests
```

## 手順2: 認証トークンを取得する

まず、CTFdのインスタンスにログインし、ProfileのAccessTokensからAPIトークンを生成・取得します。

## 手順3: Pythonスクリプトを作成する

以下のPythonスクリプトは、CTFdのAPIを利用して新しい問題を追加する例を示しています。このスクリプトは、認証トークンとCTFdのURLを含む必要があります。

```python
import requests
import json

# CTFdのURLと認証トークンを設定する
ctfd_url = 'http://localhost:8000'
api_token = 'your_api_token_here'
# 新しい問題のデータ
new_challenge = {
    'name': 'Example Challenge4',
    'category': 'Misc',
    'description': 'This is an example challenge. Submit the CTF{flag}.',
    'value': 1000,
    'type': 'standard',
    'state': 'visible'
}

# ヘッダーを設定する
headers = {
    'Authorization': f'Token {api_token}',
    'Content-Type': 'application/json',
}

# 問題を作成するリクエストを送信する
response = requests.post(
    f'{ctfd_url}/api/v1/challenges',
    headers=headers,
    json=new_challenge
)

if response.status_code == 200:
    challenge_data = response.json()
    challenge_id = challenge_data['data']['id']
    
    # フラグのデータ
    new_flag = {
        'challenge_id': challenge_id,
        'content': 'CTF{flag}',
        'type': 'static'
    }
    
    # 作成した問題にフラグを追加するリクエストを送信する
    flag_response = requests.post(
        f'{ctfd_url}/api/v1/flags',
        headers=headers,
        json=new_flag
    )
    
    if flag_response.status_code == 200:
        print("問題とフラグが正常に作成されました。")
        print(flag_response.text)
    else:
        print("フラグの追加に失敗しました。")
        print(flag_response.text)
else:
    print("問題の作成に失敗しました。")
    print(response.text)
```

このスクリプトを実行すると、指定した詳細で新しい問題がCTFdに追加されます。
しっかりやるときはyamlに書いてまとめて動かせるctfcliとやらを使うと良いみたいです。

これでAPIを利用してCTFdに問題を追加する方法を学びました。この方法を利用することで、大量の問題を効率的に追加したり、他のプログラムと統合したりすることができます。
