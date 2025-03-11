---
title: python插件
date: 2024-12-30 09:23:10
tags: plugin
categories: python
---

收集一些 有趣的 python 插件

### [MarkItDown](https://github.com/microsoft/markitdown)

微软官方推出的工具，将各种格式的文件（主要是 Office 文件）转成 Markdown 格式。标题是 GitHub 仓库链接，下面是[线上体验](https://markitdown.pro/)。除了python 命令式，还支持api方式调用

<!--more-->

```python
from markitdown import MarkItDown

md = MarkItDown()
result = md.convert("test.xlsx")
print(result.text_content)
```

支持的格式

- PDF
- PowerPoint
- Word
- Excel
- Images (EXIF metadata and OCR)
- Audio (EXIF metadata and speech transcription)
- HTML
- Text-based formats (CSV, JSON, XML)
- ZIP files (iterates over contents)