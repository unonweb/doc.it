---
title: Markdown
---

## Headings

```md
# Heading level 1
## Heading level 2
### Heading level 3
#### Heading level 4
##### Heading level 5
```

## Paragraphs
To create paragraphs, use a blank line to separate one or more lines of text.

*I really like using Markdown.*

*I think I'll use it to format all of my documents from now on.*

## Line Breaks
To create a line break (`<br>`), end a line with **two or more spaces**, and then type return.

*This is the first line.  
And this is the second line.*

### Line Break Best Practices

First line with two spaces after.  
And the next line.

First line with the HTML tag after.<br>
And the next line.

## Emphasize

### Bold
`**bold text**` --> **bold text**
`Love**is**bold` --> Love**is**bold

### Italic
`the cat's *meow*` --> the cat's *meow*

### Bold and Italic
`This text is ***really important***.` --> This text is ***really important***.

## Blockquotes
To create a blockquote, add a `>` in front of a paragraph.
> Dorothy followed her through many of the beautiful rooms in her castle.  

### Nested Blockquotes
Blockquotes can be nested. Add a `>>` in front of the paragraph you want to nest.
> Dorothy followed her through many of the beautiful rooms in her castle.
>
>> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.

## Lists

### Ordered Lists
1. First item
2. Second item
3. Third item
4. Fourth item 

### Unordered Lists
* First item
* Second item
* Third item
* Fourth item 


## Code Blocks
To create code blocks, indent every line of the block by at least four spaces or one tab.

  <html>
    <head>
    </head>
  </html>

### ... within lists
Code blocks are normally indented four spaces or one tab. When they’re in a list, indent them **eight spaces or two tabs**.  

1. *Open the file.*
2. *Find the following code block on line 21:*

        <html>
          <head>
            <title>Test</title>
          </head>

3. *Update the title to match the name of your website.*

## Code
\`nano\` --> `nano`  

### Horizontal Rules
Use three or more asterisks (`***`), dashes (`---`), or underscores (`___`) on a line by themselves.
For compatibility, put blank lines before and after horizontal rules.

## Links
`[Duck Duck Go](https://duckduckgo.com)`. --> [Duck Duck Go](https://duckduckgo.com).

### Adding Titles
You can optionally add a tooltip that appears when the user hovers over the link.  
`[Duck Duck Go](https://duckduckgo.com "The best search engine for privacy")`. --> [Duck Duck Go](https://duckduckgo.com "The best search engine for privacy")


## Images
`![alt text](path.jpg "tooltip")`

### Linking Images
`[![alt text](path.jpg "tooltip")](url)`

## CSS classes
`test {.test}` --> test {.test}

## Escaping Characters
To display a literal character that would otherwise be used to format text in a Markdown document, add a backslash in front of the character.  
`\*`
\* Without the backslash, this would be a bullet in an unordered list.

