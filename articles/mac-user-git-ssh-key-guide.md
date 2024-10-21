---
title: SSH KeyでGitHubレポジトリをクローンする初期設定【Mac用】
emoji: 🔑
type: tech
topics:
  - GitHub
  - Git
published: true
---
## 前提

- GitHubアカウントを持っている
- MacにGitをインストール済み（まだの場合は[こちら](https://git-scm.com/downloads)から）
- ターミナルが使える

## 1. SSH Key を作成

ターミナルに下記のコマンドを入れましょう。`email@example.com` のぶぶを自分のGitHubアカウントのメールアドレスに変えてから実行してください。

```bash
 ssh-keygen -t rsa -b 4096 -C "email@example.com"
```

SSH key の置き場所を聞かれますが、デフォルトのままでOKなのでそのまま `enter` を押してください。

次に `passphrase` を聞かれます。作成するかは自由ですが、セキュリティを上げたいのであれば作ることをおすすめします。入力中はカーソルが見えませんが、入力が終わったら`enter` で確認に進みましょう。

## 2. SSH Key を SSH Agent に追加

MacのSSH Agentを起動しましょう。

```bash
eval "$(ssh-agent -s)"
```

先ほど作成した SSH Key を追加。（デフォルトの場所でない場合は `ssh-add` の後を自分が選択した場所へのパスへ変えましょう。）

```bash
ssh-add ~/.ssh/id_rsa
```

## 3. SSH Key を GitHub に追加

まずは自分の SSH Key をクリップボードにコピーしましょう。

```bash
pbcopy < ~/.ssh/id_rsa.pub
```

次に GitHub に行き、自分のプロフィール写真をクリック→ Settings → サイドバーの SSH and GPG keys に行ってください。

![](/images/githubsshnavigation.png)

`New SSH key` ボタンをクリックして、先ほどコピーしたキーを `key` セクションにペーストしてください。タイトルには端末名を入れると管理しやすいです。

例： 仕事用のパソコンだったら → Work Macbook SSH Key 

![](/images/sshkeyinterface.png)

`Add SSH key` をクリックすればセットアップ完了です。