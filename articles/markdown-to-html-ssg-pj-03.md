---
title: H1をHTMLファイルのタイトルにするプラグイン【SSG-PJ-03】
emoji: ✏️
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: true
---
## 今までの流れ

Markdown→HTMLのSSGを作りたい！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-01)

Markdown→HTMLのSSGを作ってみた！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-02)

## `title`をYAMLに入れたくない（どうでもいい）こだわり

めんどくさがりやの自分は`title`をわざわざYAMLに書き出すのが面倒だと思ってます。（だからプラグイン作るはめになってるけど、どっちの方がめんどくさいの？っていうツッコミは置いといて・・・）データが二重になるし、なんかやなんですよね。なので、markdown内の`h1`を自動的にHTMLの`title`に変換してくれるプラグインを作りました。

## アウトプットイメージ

```md 
# This is the title 
content content content
```

```html
<html>
	<head>
		<title>This is the title</title>
	</head>
	<body>
		<h1>This is the title</h1>
		<p>content content content</p>
	</body>
</html>
```

## 前回作成した`parseMarkdown.js`にプラグインを追加

下記のコードを初期設定後かつMarkdownをHTMLに変換する関数の前に入れてください。

```js: parseMarkdown.js
// H1をHTMLのタイトルに変換するプラグイン
md.use(function(md) {
	md.core.ruler.push('extract_h1_to_yaml', function(state) {
		// ファイルごとのタイトルを抽出 
		let localTitle = ''; 
		for (let i = 0; i < state.tokens.length; i++) {
			const token = state.tokens[i];
			
			// token が h1 か確認
			if (token.type === 'heading_open' && token.tag === 'h1') {
				
				// h1 のコンテンツを抽出
				const nextToken = state.tokens[i + 1]; 
				if (nextToken && nextToken.type === 'inline') {
					localTitle = nextToken.content; 
				}
				break;
			}
		}
		// title を state.env に保管
		state.env.title = localTitle;
	});
});
```

## `parseMarkdown.js`内の関数をアップデート

```diff js: parseMarkdown.js
// MarkdownをHTMLに変換する関数
function processMarkdown(content) {
	const { body, attributes } = frontMatter(content);
	const env = {};
	const html = md.render(body, env);
	
+	// If the front matter doesn't contain a title, use the extracted one
+	if (!attributes.title && env.title) {
+		attributes.title = env.title;
+	}
	
	return { html, metadata: attributes };
}
```