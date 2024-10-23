---
title: ObsidianのMarkdownメモをHTMLに変換するSSG作ってみた【SSG-PJ-02】
emoji: ♻️
type: tech
topics:
  - Obsidian
  - Markdown
  - HTML
  - SSG
published: false
---
## Initialize Project 

```bash
mkdir obsidian-ssg
cd obsidian-ssg
npm init -y
```

## Install Dependencies 

```bash 
npm install markdown-it fs-extra path chokidar front-matter
```

## Script to Parse Markdown 

```js: parseMarkdown.js
const MarkdownIt = require('markdown-it');
const frontMatter = require('front-matter');
const fs = require('fs-extra');
const path = require('path');

// Initialize markdown-it with desired plugins
const md = new MarkdownIt({
	html: true,
	linkify: true,
	typographer: true,
});

// FUNCTION TO PROCESS MARKDOWN
function processMarkdown(content) {
	// Parse front matter and body
	const { body, attributes } = frontMatter(content);
	
	// Prepare the environment
	const env = {};
	
	// Render the markdown body to HTML
	const html = md.render(body, env); 
	  
	// Return the rendered HTML along with any metadata
	return { html, metadata: attributes };
}

module.exports = { processMarkdown };
```

## Script to Generate the HTML Files 

```js: generateSite.js
const fs = require('fs-extra');
const path = require('path');
const { processMarkdown } = require('./src/services/parseMarkdown');

// Where the input markdown files are stored
const inputDir = 'content';
// Where the output html files will be stored
const outputDir = 'public';

// Ensure output directory is clean
fs.emptyDirSync(outputDir);

// Recursively read all markdown files
function generateSite() {
	const files = fs.readdirSync(inputDir);
	files.forEach(file => {
		const filePath = path.join(inputDir, file);
		
		// Only handle Markdown files
		if (file.endsWith('.md')) {
			const content = fs.readFileSync(filePath, 'utf-8');
			const { html, metadata } = processMarkdown(content);
			
			// Define output file
			const outputFilePath = path.join(outputDir, file.replace('.md', '.html'));
			
			// Generate HTML with optional metadata (title, etc.)
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
			
			// Write the HTML file to the output directory
			fs.writeFileSync(outputFilePath, finalHtml);
			console.log(`Generated ${outputFilePath}`);
		}
	});
}

generateSite();
```