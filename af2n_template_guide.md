# AF2N Template Guide

## Overview

This guide explains the template language used in Annotation Flow to Note (AF2N). NOT the templ lang of Better Notes for Zotero plugin, with is the container of AF2N.

The template language is based on a Handlebars-like syntax that allows you to dynamically generate formatted notes from Zotero item data and annotations.

## Data Structure (itemData)

The template engine works with a structured data object that contains information about Zotero items and their annotations:

```json
{
  "itemCount": 1,
  "totalAnnotations": 15,
  "totalMindAnnotations": 5,
  "items": [
    {
      "id": "item_id",
      "key": "ABCD1234",
      "title": "Sample Book Title",
      "attachments": [
        {
          "id": "attachment_id", 
          "key": "EFGH5678",
          "title": "sample_book.pdf",
          "annotations": [
            {
              "id": "annotation_id",
              "key": "IJKL9012",
              "pageNumber": 15,
              "color": "yellow",
              "text": "This is highlighted text",
              "comment": "My personal note",
              "ctrl": "#"
            }
          ],
          "mindAnnotations": [
            {
              "id": "mind_annotation_id",
              "key": "MNOP3456", 
              "pageNumber": 20,
              "color": "magenta",
              "text": "Mind map node",
              "comment": "",
              "ctrl": "#\t"
            }
          ]
        }
      ]
    }
  ]
}
```

## Template Syntax

### 1. Variable Substitution

Use double curly braces to insert values:

```handlebars
{{title}}                    <!-- Item title -->
{{key}}                      <!-- Item key -->
{{this.pageNumber}}          <!-- Current annotation page number -->
{{../title}}                 <!-- Parent item title (when inside nested loops) -->
{{../../key}}                <!-- Grandparent key (when deeply nested) -->
```

### 2. Loops

#### Basic Each Loop
```handlebars
{{#each items}}
  Item: {{this.title}}
{{/each}}
```

#### Nested Loops
```handlebars
{{#each items}}
  {{#each this.attachments}}
    File: {{this.title}}
    {{#each this.annotations}}
      Page {{this.pageNumber}}: {{this.text}}
    {{/each}}
  {{/each}}
{{/each}}
```

### 3. Conditionals

#### Simple If Statement
```handlebars
{{#if this.comment}}
  Comment: {{this.comment}}
{{/if}}
```

#### If-Else Statement
```handlebars
{{#if this.color == 'green'}}
  This is a green highlight
{{else}}
  This is not a green highlight
{{/if}}
```

#### Multiple Conditions
```handlebars
{{#if this.color == 'orange' || this.color == 'red' || this.color == 'blue'}}
  Important annotation
{{/if}}
```

### 4. Special Functions

#### escape() Function
Escapes markdown special characters:
```handlebars
{{escape(this.text)}}        <!-- Escapes *, _, `, #, [, ], (, ), >, |, ~, ^ -->
```

#### lines() Function
Splits text into lines for processing:
```handlebars
{{#each lines(this.text)}}
  Line: {{this}}
{{/each}}
```

#### has() Function
Checks if a property exists or has a specific value:
```handlebars
{{#if has(this.comment)}}
  Has comment
{{/if}}
```

### 5. Code Blocks

Create code blocks with specific syntax highlighting:
```handlebars
{{<code>markmap}}
  {{#each this.mindAnnotations}}
  {{this.ctrl}} {{escape(this.text)}}
  {{/each}}
{{<code>}}
```

### 6. Switch Statements

Handle multiple conditions:
```handlebars
{{#switch this.color}}
  {{#case 'yellow'}}
    Standard highlight
  {{/case}}
  {{#case 'green'}}
    Reference highlight  
  {{/case}}
  {{#case 'orange'}}
    Important highlight
  {{/case}}
  {{#default}}
    Other color
  {{/default}}
{{/switch}}
```

## Context Variables

Within loops, you have access to special context variables:

- `this` - Current item in the loop
- `index` - Zero-based index of current item
- `first` - Boolean, true if first item
- `last` - Boolean, true if last item  
- `parent` - Parent context data
- `../` - Access parent scope
- `../../` - Access grandparent scope

## Annotation Control Characters

Annotations can have control characters that affect formatting:

- `#` - Mind map node (CTRL_MM_NODE)
- `!` - Mind map root (CTRL_MM_ROOT)
- `@` - Concatenation (CTRL_CONCAT)
- `b` - Block formatting (CTRL_MM_BLOCK)
- `-` - List item (CTRL_LIST)
- `o` - Ordered list (CTRL_ORDER_LIST)
- `<` - End marker (CTRL_END)

## Color Values

Annotations support these color values:

- `yellow` - Standard highlights
- `green` / `gray` - Reference highlights (title color)
- `orange` - Important highlights
- `red` - Critical highlights
- `blue` - Information highlights
- `magenta` - Mind map highlights

## Complete Example Template

```handlebars
{{#each items}}
  {{#each this.attachments}}
# Item: [{{../title}}](zotero://select/library/items/{{../key}}) 
- File: [{{this.title}}](zotero://select/library/items/{{this.key}})

    {{#if this.mindAnnotations}}
# Outline

      {{<code>markmap}}
        {{#each this.mindAnnotations}}
        {{this.ctrl}} {{escape(this.text)}}
        {{/each}}
      {{<code>}}
    {{/if}}

# Annotations

    {{#each this.annotations}}
      {{#each lines(this.text)}}
{{#if ../color == 'green'}}>{{/if}}{{../ctrl}} {{#if ../color == 'green'}}Refer: _{{/if}}{{#if ../color == 'orange'}}**_{{/if}}{{escape(this)}}{{#if ../color == 'orange'}}_**{{/if}}{{#if ../color == 'green'}}_{{/if}}

{{#if ../color == 'orange' || ../color == 'red' || ../color == 'blue'}}[({{../../../title}}, {{../pageNumber}})](zotero://open-pdf/library/items/{{../../key}}?page={{../pageNumber}}&annotation={{../key}})
{{/if}}
      {{/each}}
      {{#if this.comment}}

      {{/if}}
      {{#each lines(this.comment)}}
My Comment: **{{this}}**
      {{/each}}

---
    {{/each}}

  {{/each}}
{{/each}}
```

## Best Practices

1. **Use escape() for user content**: Always escape annotation text and comments to prevent markdown formatting issues
2. **Handle empty values**: Use conditionals to check for empty comments or missing data
3. **Proper nesting**: Use `../` to access parent scope when inside nested loops
4. **Line handling**: Use `lines()` function to properly format multi-line text
5. **Color-based formatting**: Use color conditions to apply different formatting styles
6. **Zotero links**: Include proper Zotero URI links for navigation back to items and annotations

## Template Processing

The template engine includes several preprocessing steps:

1. **Comment removal**: Template comments are stripped out
2. **Standalone line detection**: Template tags on their own lines are handled specially
3. **Data preprocessing**: Annotations are processed to extract control characters and format data
4. **Markdown conversion**: Final output is converted from markdown to HTML for display

## Error Handling

If template rendering fails, check for:

- Unmatched braces `{{` and `}}`
- Incorrect loop syntax (`#each` without `/each`)
- Invalid property paths
- Missing data in the itemData structure

This template language provides powerful capabilities for creating customized note formats, esp in markdown format, from your Zotero annotations and research data.