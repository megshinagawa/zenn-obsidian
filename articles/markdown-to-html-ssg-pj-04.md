---
title: YAMLタグをHTML bodyに入れるプラグイン【SSG-PJ-04】
emoji: 🏷️
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: false
---
## ゴール

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

```


## Add Plugin to `parseMarkdown.js`

```js: parseMarkdown.js
// Helper function to extract tag names from `[[tag]]` format
function extractTagsFromBrackets(tags) {
	return tags
		.map(tag => tag.toString().replace(/\[\[(.*?)\]\]/, '$1').trim())
		.filter(Boolean); // Remove empty strings
}

// Plugin to insert topical tags as links at the top of the content
md.use(function(md) {
	md.core.ruler.push('insert_topicaltags', function(state) {
		// Check if tokens exist and are an array
		if (!state.tokens || !Array.isArray(state.tokens)) {
			// Early exit if there are no tokens
			return; 
		}
		if (state.env.topicaltags && state.env.topicaltags.length > 0) {
			// Ensure tags are formatted as an array
			const rawTags = Array.isArray(state.env.topicaltags) ? state.env.topicaltags : [state.env.topicaltags];
			// Extract the clean tag names
			const tags = extractTagsFromBrackets(rawTags);
			
			// Only proceed if there are valid tags
			if (tags.length > 0) { 
				// Create a new token for the paragraph containing the links
				const paragraphOpen = new state.Token('paragraph_open', 'p', 1);
				const paragraphContent = new state.Token('inline', '', 0);
				// Construct the links for each tag
				const links = tags.map(tag => `<a href="${tag}.html">${tag}</a>`);
				// Assign the links array to the paragraphContent
				paragraphContent.content = links.join(', ');
				
				// Initialize children as an array to hold link tokens
				paragraphContent.children = [];
				
				// Create child tokens for each tag
				tags.forEach((tag, index) => {
					// Opening <a> tag
					const linkToken = new state.Token('link_open', 'a', 1); 
					// Setting the href attribute
					linkToken.attrs = [['href', `${tag}.html`]]; 
					
					// Inline token for the link text
					const linkContentToken = new state.Token('text', '', 0); 
					// Set the link text (tag name)
					linkContentToken.content = tag; 
					
					// Closing </a> tag
					const linkCloseToken = new state.Token('link_close', 'a', -1); 
					
					// Push the tokens for the link into the paragraphContent's children
					paragraphContent.children.push(linkToken, linkContentToken, linkCloseToken);
					
					// Add a separator if it's not the last tag
					if (index < tags.length - 1) {
						const separator = new state.Token('text', '', 0);
						separator.content = ', ';
						paragraphContent.children.push(separator);
					}
				});
				const paragraphClose = new state.Token('paragraph_close', 'p', -1);
				
				// Insert the paragraph with the links at the very beginning of the body content
				state.tokens.unshift(paragraphOpen, paragraphContent, paragraphClose); 
			}
		}
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
	const env = {
+		topicaltags: attributes.topicaltags || []
	};
	
	// Render the markdown body to HTML
	const html = md.render(body, env);
	
	// If the front matter doesn't contain a title, use the extracted one
	if (!attributes.title && env.title) {
		attributes.title = env.title;
	}
	
	// Return the rendered HTML along with any metadata
	return { html, metadata: attributes };
}

```