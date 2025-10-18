# AF2N - Annotation Flow to Note

## 0. Plugin Effects

When reading, the color-highlighted annotations made casually:

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315301_Pasted%20image%2020251018135908.png)

The effect of notes generated in Zotero:

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315304_Pasted%20image%2020251017200419.png)

Effect of pasting into Obsidian via clipboard:

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315304_Pasted%20image%2020251017201146.png)

## 1. Introduction

AF2N Template, which implements a simpler handlebar-style template engine at the underlying level that can be used within the Better Notes template.

When reading literature through Zotero, I can't help but annotate the content for future review and organization.

To export multiple highlight annotations from Zotero entry attachments (pdf, epub, html snapshots) into a single note. One approach is through programming, a simpler method is to customize a BN template.

The built-in template Quick Note v5 in the BN plugin can read all annotations of a document and generate a note exclusive to the entry. However, its implementation is somewhat simplistic: annotations are laid out flatly in the result, and each annotation is accompanied by a URL link, which is not conducive to reading or for LLM document embedding processing.

If you want to create a BN template according to your desired note output format, you generally still need basic JS programming knowledge, as well as a deeper understanding of the Zotero and BN plugin environment. Otherwise, the resulting output will lack interactivity, and writing these JS scripts is not easy to debug.

AF2N addresses this issue by integrating basic syntax similar to handlebars, supporting iteration over each annotation of the Item attachment within AF2N's embedded template language, enabling simpler and more direct control over the output content.

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315304_Pasted%20image%2020251018132541.png)

You can think of AF2N as "another simple template engine embedded within the Better Notes template."  
In most cases, if you want to customize the generated note format, you can modify the markdownTemplate variable in the template JS of the BN plugin itself.  
(Line 1424 in the image below)

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251018133229.png)

## 2. Usage Philosophy

Compared to the built-in annotation pipeline output of the BN plugin,  
AF2N is more friendly to brain maps and article structures.  
By focusing on the meaning of different annotation highlight colors,  
users can control the final output style of the note.

Users only need to make full use of color annotations while reading, and when necessary, start typing simple control characters via comment, without frequently switching between the article content and notes. The article view (pdf, html, and epub) and the highlighted color annotations within it are the only sources of target knowledge and records that need to be maintained. Ultimately, the note is merely the generated product.

Except for a small number of control characters, users can normally use comment.

AF2N is suitable for reading books and long texts, eliminating a large amount of irrelevant cognitive load, not just for papers.

## 3. Highlight Colors and Control Significance of Characters in Comments

### 3.1 Color Meanings

- **Yellow**: Regular annotation  
- **Red**: Regular annotation, with reference link attached in the generated note  
- **Green**: Quotation  
- **Blue**: Quotation, with reference link attached in the generated note  
- **Magenta**: Mind map  
- **Orange**: Golden sentence, automatically attached with a reference link  
- **Gray**: Title separator

### 3.2 Mind Map Marking Rules

**Magenta marking** is used for mind map clue segmentation:

- `#` represents the first level  
- `##` represents the second level  
- `!` represents the root (optional)

The magenta-colored section appears in the mind map.  
In the source code of the AF2N template, the mind map is displayed at the beginning of the generated note, using the markmap format.  
This format cannot be parsed by Zotero, but in Obsidian, the Mindmap Nextgen plugin can assist in displaying it.  
`b` represents block, which is converted to a tab in the mind map.  
`bb` is converted to two tabs.

Based on MarkMap rendering, in the mind map:

- If block and list are at the same level, the list will be ignored  
- All unmarked content will be ignored  
- If block and # are at the same level, the block will be ignored

Annotate mind maps in the literature reading interface:

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251017210302.png)

Display effect in Obsidian:

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251017210430.png)

### 3.3 Text Organization Rules

Body section, use gray annotations to organize chapters, you can use the `#` character in comments to control the hierarchy, and add custom section numbers after `#` for a clearer display.

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251017205533.png)

The presentation of the above annotation in the note:

![|300](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251017205735.png)

### 3.4 Annotation Splicing

AF2N template supports concatenating multiple adjacent, same-color annotations into a single record in the resulting note.

- `@` represents concatenating scattered phrases backward into one output item
- `@<` indicates the end of concatenation

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315305_Pasted%20image%2020251017204310.png)

### 3.5 Concatenating Multiple Annotations into a List

In the comment, add the following control characters to concatenate multiple adjacent annotations of the same color into one note record:

- `-` indicates the start of a list  
- `--` indicates list content  
- `o` indicates the start of an ordered list  
- `oo` or specific numbers indicate ordered list content, where `oo` will be automatically processed as ordered list numbering

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315306_Pasted%20image%2020251017204944.png)

The presentation of the above annotation in the note:

![|300](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315306_Pasted%20image%2020251017205126.png)

## 4. Output Effect

According to the built-in AF2N template, achieve the following effects (easily modifiable):

- **Examples, Cases**: Use italics `_` to wrap citations in the output  
- **Self-written comments**: Use `**` to wrap  
- **Golden quotes**: Use `**_` to wrap

##  5. How to Use

### 5.1 Installation to Better Notes Plugin Configuration

1. Ensure Zotero 7 is installed, and install the Better Notes plugin (version 2.5.8 or above)
2. Open Zotero, enter the Better Notes plugin settings
3. Import the `bn-plugin/annotation-flow-to-note-1.bn` file in the template configuration
4. Modify the behavior of the AF2N embedded template in the source code to fine-tune your desired output style. (Optional, WYSIWYG)
5. Configure the plugin to copy the resulting md content to the clipboard during runtime. (Optional)

### 5.2 How to Change the Default AF2N

1. Refer to `af2n_template_guide.md` for AF2N template syntax. (An example is in `note.templ` )

2. Modify the JS code already imported into the Better Notes plugin. (Around line 1400 of the source file, the assignment of the variable markdownTemplate)

### 5.3 How to Copy the Resulting md to the Clipboard

1. Uncomment the following three lines in the source code (lines 1775～1777)

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768315306_Pasted%20image%2020251017211917.png)

2. Run the AF2N template in Better Notes
3. The generated Markdown content will be automatically formatted and copied to the clipboard
4. It can be directly pasted into other Markdown editors, including Obsidian

##  6. Project Structure

```
zotero-af2n/
├── README.md                           # Project documentation
├── note.templ                          # AF2N template file
├── af2n_template_guide.md             # Template syntax guide
└── bn-plugin/

└── annotation-flow-to-note-1.bn   # contents to be imported to Better Notes template plugin
```

##  7. Support Author

- Blog: [AI Decameron](https://blog.aidecameron.com)
- Wechat Page:

<img src="https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760165896715_wechat_aidecameron.JPG?raw=true" width="20%"/>

## 8. License

MIT
