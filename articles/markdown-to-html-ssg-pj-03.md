---
title: H1をHTMLファイルのタイトルにするプラグイン【SSG-PJ-03】
emoji: ✏️
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: false
---
## Add Plugin to `parseMarkdown.js`

```js: parseMarkdown.js
// Plugin to extract H1 as title without removing it from the content
md.use(function(md) {
	md.core.ruler.push('extract_h1_to_yaml', function(state) {
		// Title is local to each file 
		let localTitle = ''; 
		for (let i = 0; i < state.tokens.length; i++) {
			const token = state.tokens[i];
			
			// Check if the token is an h1 heading
			if (token.type === 'heading_open' && token.tag === 'h1') {
				
				// The inline content of the h1
				const nextToken = state.tokens[i + 1]; 
				
				if (nextToken && nextToken.type === 'inline') {
					// Extract the title text
					localTitle = nextToken.content; 
				}
				break;
			}
		}
		// Store the title in the environment (state.env)
		state.env.title = localTitle;
	});
});
```

### Update the Function to Process Markdown 

```diff js: parseMarkdown.js

// FUNCTION TO PROCESS MARKDOWN
function processMarkdown(content) {
	
	// Parse front matter and body
	const { body, attributes } = frontMatter(content);
	
	// Prepare the environment
	const env = {};
	
	// Render the markdown body to HTML
	const html = md.render(body, env);
	
+	// If the front matter doesn't contain a title, use the extracted one
+	if (!attributes.title && env.title) {
+		attributes.title = env.title;
+	}
	
	// Return the rendered HTML along with any metadata
	return { html, metadata: attributes };
}

```