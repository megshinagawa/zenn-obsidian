---
title: "[[wikilink]]をHTMLリンクに変換するプラグイン【SSG-PJ-05】"
emoji: 🔗
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: true
---
## これまでの流れ

Markdown→HTMLのSSGを作りたい！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-01)

Markdown→HTMLのSSGを作ってみた！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-02)

タイトルを抽出するプラグインを作ってみた！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-03)

YAMLタグを変換するプラグインを作ってみた！
@[card](https://zenn.dev/megshinagawa/articles/markdown-to-html-ssg-pj-04)

## アウトプットイメージ

Obsidian内ではノートとノートの間を繋げるのに wikilink を使います。Wikilinkは普通のmarkdownでは使われないため、今のままだとただのテキストとして変換されてしまいます。なので今回はノート内の wikilink をHTMLリンクに変換するプラグインを作成しましょう！

```md
# HEADER 1 
content content content [[wikilink]]
```

```html
<html>
	<head>
		<title>HEADER 1</title>
	</head>
	<body>
		<h1>HEADER 1</h1>
		<p>content content content <a href="wikilink.html">wikilink</a></p>
	</body>
</html>
```

## `parseMarkdown.js`にプラグインを追加

下記のコードを初期設定後かつMarkdownをHTMLに変換する関数の前に入れてください。

```js: parseMarkdown.js
// [[WIKILINKS]] を変換するプラグイン
md.use(function(md) {
	md.core.ruler.push('wikilinks', function(state) {
		for (let i = 0; i < state.tokens.length; i++) {
		const token = state.tokens[i];
		if (token.type === 'inline') {
			for (let j = 0; j < token.children.length; j++) {
				const childToken = token.children[j];
				if (childToken.type === 'text') {
					const wikilinkPattern = /\[\[(.*?)\]\]/g;
					const matches = childToken.content.match(wikilinkPattern);
					if (matches) {
						const newChildren = [];
						let lastIndex = 0;
						matches.forEach((match) => {
							const linkText = match.slice(2, -2).trim();
							const index = childToken.content.indexOf(match, lastIndex);
							if (index > lastIndex) {
								newChildren.push(new state.Token('text', '', 0, childToken.content.slice(lastIndex, index)));
							}
							const linkOpen = new state.Token('link_open', 'a', 1);
							linkOpen.attrs = [['href', `${linkText}.html`]];
							const linkContent = new state.Token('text', '', 0);
							linkContent.content = linkText;
							const linkClose = new state.Token('link_close', 'a', -1); 
							newChildren.push(linkOpen, linkContent, linkClose);
							lastIndex = index + match.length;
							});
							if (lastIndex < childToken.content.length) {
								newChildren.push(new state.Token('text', '', 0, childToken.content.slice(lastIndex)));
							}
							token.children.splice(j, 1, ...newChildren);
						}
					}
				}
			}
		}
	});
});
```
