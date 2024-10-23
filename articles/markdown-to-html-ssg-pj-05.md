---
title: "[[wikilink]]をHTMLリンクに変換するプラグイン【SSG-PJ-05】"
emoji: 🔗
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
// PLUGIN TO HANDLE [[WIKILINKS]]

md.use(function(md) {

md.core.ruler.push('wikilinks', function(state) {

// Loop through all tokens in the state

for (let i = 0; i < state.tokens.length; i++) {

const token = state.tokens[i];

  

// Check if the token type is 'inline' (where text content is processed)

if (token.type === 'inline') {

// Loop through the children of the inline token

for (let j = 0; j < token.children.length; j++) {

const childToken = token.children[j];

  

// Check if the child token type is 'text'

if (childToken.type === 'text') {

// Use regex to find [[wikilinks]] in the text

const wikilinkPattern = /\[\[(.*?)\]\]/g;

const matches = childToken.content.match(wikilinkPattern);

  

if (matches) {

// Create a new array to hold the modified children

const newChildren = [];

let lastIndex = 0;

  

// Process each match found

matches.forEach((match) => {

const linkText = match.slice(2, -2).trim(); // Get the text between [[ and ]]

// Add the text before the wikilink to the new children

const index = childToken.content.indexOf(match, lastIndex);

if (index > lastIndex) {

newChildren.push(new state.Token('text', '', 0, childToken.content.slice(lastIndex, index)));

}

  

// Create the link token

const linkOpen = new state.Token('link_open', 'a', 1);

linkOpen.attrs = [['href', `${linkText}.html`]]; // Set the href for the link

  

// Add the link text as a token

const linkContent = new state.Token('text', '', 0);

linkContent.content = linkText;

  

const linkClose = new state.Token('link_close', 'a', -1); // Closing link token

  

// Push the link tokens to the new children

newChildren.push(linkOpen, linkContent, linkClose);

  

// Update the last index to track the end of the current match

lastIndex = index + match.length;

});

  

// Add the remaining text after the last wikilink to new children

if (lastIndex < childToken.content.length) {

newChildren.push(new state.Token('text', '', 0, childToken.content.slice(lastIndex)));

}

  

// Replace original children with the new ones

token.children.splice(j, 1, ...newChildren);

}

}

}

}

}

});

});
```
