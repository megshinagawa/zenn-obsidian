---
title: Zennの記事をローカルで書く方法【Zenn初心者向け】
emoji: 🔰
type: tech
topics:
  - Zenn
  - Git
  - GitHub
  - Nodejs
published: false
---
## ゴール

**Zennの記事をWebエディターではなく、自分の使い慣れたコードエディタで書きたい！**

この記事では自分が実際にどうやってこの記事をVSCodeで書けるようにセットアップしたかを紹介します。

*注：自分はMacユーザーなので、解説はMac使用になります。ご了承ください。*

## 前提

- GitHub を使ったことがある：GitHub のアカウント作成、初期設定などは解説しないです
- Terminal 操作をしたことがある：全て理解していなくても、Terminal が何かわかっている前提で解説を進めます

## 1. まずはZennをGitHubと連携する

このステップは、Zenn公式アカウントが解説している記事がわかりやすいので、それをフォローしていただけるとスムーズだと思います。

@[card](https://zenn.dev/zenn/articles/connect-to-github)

上記のステップが完了してから次に進んでください！

## 2. GitHubレポジトリをローカルで触れるようにする

GitHub のレポジトリをローカルにクローンすることで、記事などを自分の使いやすいコード・テキストエディタで作成することができます。

### レポジトリの場所を決める

先ほど作成したレポジトリを自分のPCのどこに置くかを最初に決めましょう。

自分は `home directory` に `Zenn` というフォルダを作成しました。Finder から直接作成するのもありですが、ここからは Terminal を使う作業もあるので、Terminal を使って作成することをおすすめします。やり方はこんな感じ：

```bash
cd ~ # home directory へ移動
mkdir Zenn # Zenn というフォルダを作成
cd Zenn # Zenn フォルダに移動
```

### GitHub レポジトリをクローンする

場所が決まったら、Terminal 上でそのフォルダへ移動して、そこに GitHub レポジトリをクローンしましょう。レポジトリのリンクは人それぞれ違うので `<GIT-HUB-REPO-LINK-HERE>` と記載しました。

```bash
git clone <GIT-HUB-REPO-LINK-HERE>
```

## 3. Zenn CLI をインストールする

### Zenn CLI とは？

> ローカルでの執筆時には、スムーズにmarkdownファイルの作成したり、コンテンツをプレビューしたりするために「Zenn CLI」を導入しましょう。
> -- [Zenn公式アカウント](https://zenn.dev/zenn/articles/install-zenn-cli)

### node.js のバージョンを確認

Zenn CLI を使うには node.js が必要なので、まずは node.js がインストールされているか確認しましょう。

```bash
node -v
```

これで `v20.5.0` みたいな数字が出てくればOK！

（`v14`未満の場合はアップデートしてください！）

出てこない場合はインストールが必要です。
#### node.js をインストール

初めてインストールする方は下記のリンクからダウンロードしましょう！

@[card](https://nodejs.org/en/)


インストール完了後、下記のコマンドでバージョン確認を行ってください。

```bash
node -v
```

今回はちゃんと数字が出てくるはずです！

### いよいよ Zenn CLI をインストール

node.js の確認も済んだので、Zenn CLI をインストールしましょう！

Terminal をリセットされた場合、もう一度自分のレポジトリのフォルダまで移動してください。

```bash
cd Zenn/zenn-content
```

そこで、プロジェクトのデフォルト設定と Zenn CLI のインストールを行いましょう。

```bash
npm init --yes # プロジェクトをデフォルト設定で初期化
npm install zenn-cli # zenn-cliをインストール
```

その後、Zenn用のセットアップを行いましょう。

```bash
npx zenn init
```

これで Zenn CLI のインストールは完了です！

## 4. Zenn CLI コマンドリスト

### 新規記事作成
```bash
#ランダムなスラッグで記事が作成される
npx zenn new:article

# スラッグなどを細かく最初から設定したい場合
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
# 注：slug はa-z0-9、ハイフン-、アンダースコア_の 12〜50 字の組み合わせにする必要があります
```

### プレビュー画面を開く

```bash
npx zenn preview
```

### その他

@[card](https://zenn.dev/zenn/articles/zenn-cli-guide)

## まとめ

これでセットアップは完了です！

これから新規記事を作成したい時はコマンドを入れて、自分の好きなコード・テキストエディタで編集するだけです！

初めて書いた記事ですが、もし役に立ったと思ったらいいねとフォローお願いします！