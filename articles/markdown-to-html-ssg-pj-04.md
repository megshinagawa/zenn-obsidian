---
title: YAMLタグをHTML bodyに入れるプラグイン【SSG-PJ-04】
emoji: 🏷️
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

## アウトプットイメージ

自分はObsidianのタグ機能は使わず、自作の`topicaltags`をYAML内で使っています。`topicaltags`はタグ自体が他のメモへのリンクなので、HTMLに変換後もリンクとして扱う必要があります。イメージはこんな感じです：

```markdown 
---
topicaltags:
	- [[tag1]]
	- [[tag2]]
---

# HEADER 1 

content content content 
```

```html
<html>
	<head>
		<title>HEADER 1</title>
	</head>
	<body>
		<h1>HEADER 1</h1>
		<p><a href="tag1.html">tag1</a>, <a href="tag2.html">tag2</a></p>
		<p>content content content</p>
	</body>
</html>
```

## `parseMarkdown.js`にプラグインを追加

下記のコードを初期設定後かつMarkdownをHTMLに変換する関数の前に入れてください。

```js: parseMarkdown.js
// タグの中身を取り出す関数
function extractTagsFromBrackets(tags) {
	return tags
	.map(tag => tag.toString().replace(/\[\[(.*?)\]\]/, '$1').trim()) 
	.filter(Boolean); 
}

// YAMLタグをH1の後に追加するプラグイン
md.use(function(md) {
	md.core.ruler.push('insert_topicaltags', function(state) {
		if (!state.tokens || !Array.isArray(state.tokens)) {
			return; // Early exit if there are no tokens
		}
		if (state.env.topicaltags && state.env.topicaltags.length > 0) {
			const rawTags = Array.isArray(state.env.topicaltags) ? state.env.topicaltags : [state.env.topicaltags];
			const tags = extractTagsFromBrackets(rawTags);
			if (tags.length > 0) {
				const paragraphOpen = new state.Token('paragraph_open', 'p', 1);
				const paragraphContent = new state.Token('inline', '', 0);
				const links = tags.map(tag => `<a href="${tag}.html">${tag}</a>`);	
				paragraphContent.content = links.join(', ');
				paragraphContent.children = [];
				tags.forEach((tag, index) => {
					const linkToken = new state.Token('link_open', 'a', 1);
					linkToken.attrs = [['href', `${tag}`]]; 
					const linkContentToken = new state.Token('text', '', 0);
					linkContentToken.content = tag; 
					const linkCloseToken = new state.Token('link_close', 'a', -1); 
					paragraphContent.children.push(linkToken, linkContentToken, linkCloseToken);
					if (index < tags.length - 1) {
						const separator = new state.Token('text', '', 0);
						separator.content = ', '; 
						paragraphContent.children.push(separator);
					}
				});
				const paragraphClose = new state.Token('paragraph_close', 'p', -1);
				const h1Index = state.tokens.findIndex(token => token.type === 'heading_open' && token.tag === 'h1');
				if (h1Index !== -1) {
					state.tokens.splice(h1Index + 3, 0, paragraphOpen, paragraphContent, paragraphClose);
				} else {
					console.warn("No H1 tag found to insert topical tags after.");
				}
			}
		}
	});
});
```

## `parseMarkdown.js`内の関数をアップデート

```diff js: parseMarkdown.js
// MarkdownをHTMLに変換する関数
function processMarkdown(content) {
	const { body, attributes } = frontMatter(content);
	const env = {
+		topicaltags: attributes.topicaltags || []
	};
	const html = md.render(body, env);
	if (!attributes.title && env.title) {
		attributes.title = env.title;
	}
	return { html, metadata: attributes };
}
```