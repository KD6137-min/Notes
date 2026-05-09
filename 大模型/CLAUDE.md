# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a personal technical knowledge base written in Chinese and English, organized as Markdown notes. There are no build systems, tests, or deployable code.

## Structure

```
Notes/
├── Python笔记/          # Python: syntax, OOP, concurrency, web, data analysis
│   └── 数据分析/        # NumPy, Pandas, Matplotlib
│   └── 网络编程/        # Django, Flask, web scraping
├── 深度学习/            # Deep learning: CNN, RNN, Transformer, attention, math
│   └── torch/           # PyTorch API reference by topic
├── 机器学习/            # ML fundamentals and Scikit-Learn
├── 前端开发/            # HTML, CSS, JavaScript, jQuery, frontend frameworks
│   └── JavaScript/
├── 数据库/              # SQL, Redis, Hadoop
├── 编程语言/            # C, C++, Java, Go
├── 辅助工具/            # Tools: Git, Docker, Shell, SSH, Linux, LaTeX, Markdown, etc.
├── 读书笔记/            # Book notes: Python Cookbook, design patterns, etc.
├── misc.md              # Miscellaneous notes
├── Ultralytics.md       # Ultralytics/YOLO notes
└── 英语.md              # English vocabulary/phrases
```

## Conventions

- Notes are written primarily in Chinese with technical terms in English.
- Code blocks use fenced markdown with language tags (e.g., ` ```python `).
- Each note file covers a single topic or library; sections are organized with `##` and `###` headings.
- `assets/` stores images referenced in notes.
- The `torch/` subdirectory uses numbered files (`1.核心张量计算.md`, etc.) with an `index.md` table of contents.

## Working with This Repo

When adding or editing notes:
- Follow the existing file naming convention (Chinese topic name or English tool name).
- Place new notes in the appropriate subdirectory based on topic.
- Use the same heading depth and code-block style as surrounding files.
- The `misc.md` file is for notes that don't fit elsewhere.
