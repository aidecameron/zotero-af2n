# AF2N - Annotation Flow to Note

## 0.插件效果

阅读时，随手标记的颜色高亮的 annotation：

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391559_Pasted%20image%2020251018135908.png)

在 Zotero 中生成的笔记效果：

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391560_Pasted%20image%2020251017200419.png)

通过剪贴板粘贴到 Obsidian 的效果：

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391560_Pasted%20image%2020251017201146.png)

## 1. 简介

AF2N 模版，底层实现了一个在 Better Notes 模版中可使用的，更简单的 handlebar 风格模版引擎。

在通过 Zotero 阅读文献时，忍不住要标注其中内容，以便后续回顾和组织。

要把 Zotero 条目附件（pdf、epub、html 快照）的多个高亮 annotation 输出为一个笔记。一种方法是编程，更简单的方式是， 定制一个 BN 模版。

BN 插件内置的模版 Quick Note v5，可将一个文档的所有 annotation 读出，生成一个条目专属的笔记。但它的实现稍显简单，annotation 在结果中平铺式排开，并且每个 annotation 都附加了 url 链接，不利于阅读，也不利于 LLM 做文档嵌入处理。

如果要按自己想要的笔记输出格式写一个 BN 模版，通常还是需要基本的JS编程知识，以及对 Zotero、BN 插件环境较为深入的了解。否则出来的结果互动性不强，写这些 JS 也并不易于调试。

AF2N 解决这个问题，它集成类似 handlebar 的基本语法，支持在 AF2N 内嵌的模版语言中迭代 Item attachment 的各个 annotation，形成对输出内容更简单、直接的控制。

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391560_Pasted%20image%2020251018132541.png)

你可以把 AF2N 看作“嵌在 Better Notes 模版中的另一个简单模版引擎”。大多数情况下，如果要自定义生成的笔记格式，修改 BN 插件本模版 JS 中的 markdownTemplate 变量就可以。（下图中的 1424 行）

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251018133229.png)

## 2. 使用理念

相对于 BN 插件 内置的 annotation 流水式输出。AF2N 对脑图和文章结构更为友好，用户关注不同 annotation 高亮颜色的意义，就可以控制最终 note 的输出风格。

用户只需在阅读时充分利用颜色标注，必要的时候通过 comment 开始键入简单的控制字符，并不需要在文章内容和 note 之间频繁跳转。文章视图（pdf、html 和 epub）以及其中的高亮颜色 annotation，是唯一需要维护的目标知识和记录源头。最后 note 只是生成的产品。

除少量的控制字符外，用户可以正常使用 comment。

AF2N 适配于书籍和长文的阅读，消除大量无关认知负荷，而不仅是论文。

## 3. 高亮颜色和 comment 中字符的控制意义

###  3.1 颜色含义

- **黄色**：普通 annotation
- **红色**：普通 annotation，在生成的 note 中附加引用链接
- **绿色**：引语
- **蓝色**：引语，在生成的 note 中附加引用链接
- **洋红色**：脑图
- **橙色**：金句，自动附加引用链接
- **灰色**：标题分割

### 3.2 脑图标记规则

**洋红色标注**用于脑图线索分割：

- `#` 代表第一级
- `##` 代表第二级
- `!` 代表根（可选）

洋红色的部分出现在脑图。在 AF2N 模版的源码中，脑图会在生成 note 的开头展现，使用 markmap 格式，这个格式 Zotero 无法解析，但在 Obsidian 中，Mindmap Nextgen 插件可辅助显示。`b` 代表 block，在脑图中转为 tab。`bb` 转为两个 tab。

基于 MarkMap 的渲染，在脑图中：

- 如果 block 和 list 同级，list 会被忽略掉
- 所有无标记的内容会被忽略掉
- 如果有 block 和 # 同级，block 会被忽略掉

在文献阅读界面标注脑图：

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251017210302.png)

在 Obsidian 中的显示效果：

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251017210430.png)

### 3.3 正文组织规则

正文部分，用灰色的 annotation 组织章节，可在 comment 中使用 `#` 字符控制层级，并在 `#` 后加入自定义的节号以获得更为清晰的显示。

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251017205533.png)

以上 annotation 在 note 中的展现：

![|300](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251017205735.png)

### 3.4 annotation 拼接

AF2N 模版支持将多个相邻的，同色的 annotation 在结果 note 中拼接为一条记录。

- `@` 代表向后拼接散句为一个输出项
- `@<` 表示拼接结束

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391561_Pasted%20image%2020251017204310.png)

### 3.5 多条 annotation 拼接为列表

在 comment 中，加入如下控制字符，可拼接多个同色相邻 annotation 为一条笔记记录：

- `-` 表示 list 开始
- `--` 表示 list 内容
- `o` 表示有序 list 开始
- `oo` 或者具体数字，表示有序 list 内容，oo 会被自动处理为有序 list 序号

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391562_Pasted%20image%2020251017204944.png)

以上 annotation 在 note 中的展现：

![|300](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391562_Pasted%20image%2020251017205126.png)

## 4. 输出效果

根据自带的 AF2N 模板，实现以下效果（可轻易更改）：

- **引例、案例**：在输出中用斜体 `_` 包裹引用
- **自己写的评论**：用 `**` 包裹
- **金句名言**：用 `**_` 包裹

##  5. 如何使用

### 5.1 安装到 Better Notes 插件配置

1. 确保 Zotero 7 安装，并安装 Better Notes 插件（2.5.8 或以上）
2. 打开 Zotero，进入 Better Notes 插件设置
3. 在模板配置中导入 `bn-plugin/annotation-flow-to-note-1.bn` 文件
4. 在源码修改 AF2N 内嵌模版的行为，微调你想要的输出风格。（可选，所见即所得）
5. 让这个插件在运行时，将结果的 md 内容复制到剪贴板。（可选）

### 5.2 如何更改默认的 AF2N

1. 参考 `af2n_template_guide.md` 了解 AF2N 模板语法。（一个样例在 `note.templ` ）
2. 修改已导入 Better Notes 插件的 JS 代码。（ 源文件的约 1400 多行，变量 markdownTemplate 的赋值）

### 5.3 如何拷贝结果 md 到剪贴板

1. 在源代码中打开下面三行的注释（1775～1777）

![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760768391562_Pasted%20image%2020251017211917.png)

2. 在 Better Notes 中运行 AF2N 模板
3. 生成的 Markdown 内容会自动格式化，并将内容复制到剪贴板
4. 可以直接粘贴到其他 Markdown 编辑器中，包括 Obsidian 中使用

##  6. 项目结构

```
zotero-af2n/
├── README.md                           # 项目说明文档
├── note.templ                          # AF2N 模板文件
├── af2n_template_guide.md             # 模板语法指南
└── bn-plugin/
    └── annotation-flow-to-note-1.bn   # Better Notes 插件配置文件
```

##  7. 贡献

加🌟是对作者的支持！欢迎提交 Issue 和 Pull Request 来改进这个项目。

## 支持作者

作者的公众号：**ai的十日谈**
作者的 blog：[AI Decameron](https://blog.aidecameron.com)

<img src="https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760165896715_wechat_aidecameron.JPG?raw=true" width="20%"/>

## 许可证

本项目采用 MIT 许可证。