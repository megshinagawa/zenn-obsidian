---
title: MarkdownをHTMLに変換するSSGを作りたい【SSG-PJ-01】
emoji: ♻️
type: idea
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: false
---
## ことの始まり

ADHDの自分はどうしてもメモったものを無くします。なのでウェブ上に自分のナレッジベースをアウトプットすることでいつでも見つけられる状態にしておきたいと思ったのがことの始まりです。

そうなると、自分の書き慣れているMarkdownでメモをとってそれをWebサイトか何かにアップするのがいいだろうとなりました。イメージしている理想のフローはこんな感じ：

![](/images/ssgworkflowimage.jpeg)

## 既存ツールじゃダメなの？

実はこれってもういろんな形で存在しているんですよね。Obsidianのコミュニティで有名なのはObsidian公式の [Obsidian Publish](https://obsidian.md/publish) とか、サードパーティだと [Quartz](https://quartz.jzhao.xyz/) とか。

じゃあなんで自分で作ろうとしているのか。

大きな理由としては、デプロイするWebサイトを自分好みにカスタマイズしたいと言う理由です。どうしても既存のツールだとそのツールならではの制約みたいなものがあります。（使いやすくしてくれてるから当たり前ですよね。）そのツールのカスタム方法を勉強するのもありですが、せっかくコードも書けるし・・・ 自分のスキルアップのためにやってみた方が良くない？って思ってしまったんですよね。

別に革命的なことをしようとしてるわけではないので、本当に自分の好みと学びのためにこのプロジェクトを始めたいと思っています。道中の学びもZennにまとめていこうと思うので、興味がある方はフォローしていただけると嬉しいです！

## プロジェクトのロードマップ

始める勢いはあるものの、始め方がイマイチわからなかったのでChatGPTにロードマップを作成してもらいました。それがこちら。

### 1. ファイル構造
```graphql
my-ssg/
├── src/              # Source code for your SSG
├── content/          # Markdown files (your Obsidian notes)
├── templates/        # HTML templates and CSS files
├── public/           # Output HTML files will be generated here
└── config.json       # Configuration file (optional)
```
### 2. Markdown を HTML に変換
Markdown parser を使って `.md` ファイルをHTMLに変換
### 3. HTML テンプレート
templating engine を使って変換されたコンテンツをHTMLテンプレートの中に入れる（テンプレートにはCSSやレイアウト情報が含まれる）
### 4. Command-line Interface (CLI)
Markdownファイルを読み込んでHTMLにプロセスする簡単なCLIを作成
### 5. Customization via CSS
カスタム要素をCSS経由で `templates/` に入れる