---
title: ObsidianのMarkdownメモをHTMLに変換するSSG作ってみた【SSG-PJ-02】
emoji: ♻️
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
  - Nodejs
published: true
---
## このプロジェクトのそもそも

Markdown→HTMLのSSGを作りたい！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-01)

## プロジェクト初期設定

プロジェクトには `Node.js` を使うので、一緒に作成したい方は `Node.js` がインストールされているか確認してください。（まだの場合は[こちら](https://nodejs.org/en/download/package-manager)から。）

```bash 
node -v 
```

インストール確認後、プロジェクト用のフォルダを作成して、初期設定を行いましょう。

```bash
mkdir obsidian-ssg-pj
cd obsidian-ssg-pj
npm init -y
```

## 必要と思われるライブラリをインストール

今回使うと思われるライブラリはこんな感じ：

- `markdown-it`: Markdown を HTML に変換してくれるライブラリ
- `fs-extra`: ファイルシステム操作の拡張
- `path`: パスをいじれる
- `chokidar`: 更新が必要かファイルを見守ってくれる
- `front-matter`: metadataをHTMLに変換できる

```bash 
npm install markdown-it fs-extra path chokidar front-matter
```

## フォルダ構成

```bash
/obsidian-ssg-pj
│
├── /vault               # 変換したいMarkdownファイルを入れるフォルダ 
│   ├── example1.md
│   ├── example2.md
│
├── /public              # 変換されたHTMLファイルを入れるフォルダ
│   ├── example1.html    
│   ├── example2.html
│
├── /scripts             # 変換スクリプトを入れるフォルダ
│   ├── parseMarkdown.js          
│   ├── generateSite.js           
│
└── package.json

```

## MarkdownをHTMLに変換するスクリプト

```js: parseMarkdown.js
const MarkdownIt = require('markdown-it');
const frontMatter = require('front-matter');
const fs = require('fs-extra');
const path = require('path');

// markdown-it の初期設定（プラグインも設定）
const md = new MarkdownIt({
	html: true,
	linkify: true,
	typographer: true,
});

// MarkdownをHTMLに変換する関数
function processMarkdown(content) {
	const { body, attributes } = frontMatter(content);
	const env = {};
	const html = md.render(body, env); 
	return { html, metadata: attributes };
}

module.exports = { processMarkdown };
```

## HTMLファイルを作成するスクリプト

上記のスクリプトでMarkdownをHTMLにしたものをファイルに書き出す。

```js: generateSite.js
const fs = require('fs-extra');
const path = require('path');
const { processMarkdown } = require('./parseMarkdown');

// インプットファイルの置き場所
const inputDir = 'vault';
// アウトプットファイルの置き場所
const outputDir = 'public';

// アウトプットフォルダのステータスを確認
fs.emptyDirSync(outputDir);

// 再帰的にファイルを読む
function generateSite() {
	const files = fs.readdirSync(inputDir);
	files.forEach(file => {
		const filePath = path.join(inputDir, file);
		
		// .md ファイルのみを対象とする
		if (file.endsWith('.md')) {
			const content = fs.readFileSync(filePath, 'utf-8');
			const { html, metadata } = processMarkdown(content);
			
			// アウトプットファイルを指定
			const outputFilePath = path.join(outputDir, file.replace('.md', '.html'));
			
			// HTMLを metadata を含めて生成
			const finalHtml = `
				<html>
					<head>
						<title>${metadata.title || 'Untitled'}</title>\
					</head>
					<body>
						${html}
					</body>
				</html>
			
			`;
			
			// HTMLをファイルに書き出す
			fs.writeFileSync(outputFilePath, finalHtml);
			console.log(`Generated ${outputFilePath}`);
		}
	});
}

generateSite();
```

## テスト用Markdownファイルを作成

```md: example1.md
---
title: example1
---

# サンプルファイル①

これは最初のテストファイルです。

```

```md: example2.md
---
title: example2
---

# サンプルファイル②

これは2番目のテストファイルです。

```

## スクリプトを実行

```bash 
node generateSite.js
```

`public`フォルダにHTMLファイルが作成されていれば成功です！