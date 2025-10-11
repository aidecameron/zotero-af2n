# AF2N - Annotation Flow to Note

## AF2N 模版，在 Better Notes 模版中使用另一个更简单的 handlebar 风格模版

在通过 Zotero 阅读文献时，忍不住要标注其中内容，以便后续回顾和组织。

要把 Zotero 条目附件（pdf、epub、html 快照）的标注（annotation）输出为一个笔记。一种方法是编程，更简单的方式是， 定制一个 BN 模版。

写 BN 模版通常还是需要基本的JS编程知识，否则出来的结果互动性不强。写这些 JS 也易于调试。

AF2N 解决这个问题，它集成类似 handlebar 的基本语法，支持在 AF2N 内嵌的模版语言中迭代附件的各个 annotation，形成对输出内容更简单、直接的控制。

## 使用理念

相对于BN 内嵌的 annotation 流水式输出。AF2N 对脑图和文章结构更为友好，用户关注不同标注颜色的意义，就可以控制最终 note 的输出风格。

用户只需在阅读时充分利用颜色标注，必要的时候通过 comment 渐入简单的控制。不需要在文章内容和 note 之间跳转。文章视图（pdf、html 和 epub）以及其中的颜色标注，是唯一需要维护的知识源头。最后 note 只是生成。

AF2N 适配于书籍和长文的阅读，而不仅是论文。

## 颜色和批注（comment）的意义

### 颜色标注含义

- **黄色**：普通标注
- **红色**：普通标注，自动附加引用链接
- **绿色**：数据、引言和案例
- **蓝色**：数据、引言和案例，自动附加引用链接
- **粉色**：脑图
- **橙色**：金句，自动附加引用链接
- **灰色**：标题分割

### 脑图标记规则

**粉色标注**用于脑图线索分割：
- `#` 代表第一级
- `##` 代表第二级
- `!` 代表根（可选）

粉色的部分出现在脑图，`b` 代表 block，在脑图中转为 tab。`bb` 转为两个 tab。

基于 MarkMap 的渲染，在脑图中：
- 如果 block 和 list 同级，list 会被忽略掉
- 所有无标记的内容会被忽略掉
- 如果有 block 和 # 同级，block 会被忽略掉

脑图和内容分开显示，更为简洁。

### 正文组织规则

正文部分，用灰色的 `#` 层级组织，可以在 `#` 后加入自定义的节号以获得更为清晰的显示。

- `@` 代表向后拼接散句为一个输出项
- `@<` 表示拼接结束

### Comment 中的标记

在 comment 中：
- `-` 表示 list 开始
- `--` 表示 list 内容
- `o` 表示有序 list 开始
- `oo` 或者具体数字，表示有序 list 内容，oo 会被自动处理为有序 list 序号

## 输出效果

根据自带的 AF2N 模板，实现以下效果（可轻易更改）：

- **引例、案例**：在输出中用斜体 `_` 包裹引用
- **自己写的评论**：用 `**` 包裹
- **金句**：用 `**_` 包裹

## 如何使用

### 安装到 Better Notes 插件配置

1. 打开 Zotero，进入 Better Notes 插件设置
2. 在模板配置中导入 `bn-plugin/annotation-flow-to-note-1.bn` 文件
3. 修改 AF2N 内嵌模版的行为，微调你想要的输出风格（可选，所见即所得）
4. 让这个插件在运行时，将结果的 md 内容复制到剪贴板

### 如何更改默认的 AF2N

1. 参考 `af2n_template_guide.md` 了解 AF2N 模板语法。（一个样例在 `note.templ` ）
2. 修改已导入 Better Notes 插件的 js 代码。

### 如何拷贝 md 到剪贴板

1. 在 Better Notes 中运行 AF2N 模板
2. 生成的 Markdown 内容会自动格式化
3. 使用 Zotero 的复制功能将内容复制到剪贴板
4. 可以直接粘贴到其他 Markdown 编辑器中使用

## 文件结构

```
zotero-af2n/
├── README.md                           # 项目说明文档
├── note.templ                          # AF2N 模板文件
├── af2n_template_guide.md             # 模板语法指南
└── bn-plugin/
    └── annotation-flow-to-note-1.bn   # Better Notes 插件配置文件
```

## 贡献

欢迎提交 Issue 和 Pull Request 来改进这个项目。

## 支持作者

作者的公众号：**ai的十日谈**
![](https://cdn.jsdelivr.net/gh/aidecameron/imgbed@main/blog/2025/10/1760165896715_wechat_aidecameron.JPG)

## 许可证

本项目采用 MIT 许可证。