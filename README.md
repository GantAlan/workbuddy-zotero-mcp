# WorkBuddy Zotero MCP

A portable Windows MCP server that connects WorkBuddy to Zotero Desktop through Zotero's Local API and Connector interfaces.

这是一个面向 Windows 的可迁移 Zotero 本地 MCP 服务，通过 Zotero Local API 和 Connector 接口把 WorkBuddy 接入 Zotero Desktop。

## Documentation / 文档

- [English documentation](WORKBUDDY-ZOTERO-MCP.md)
- [中文说明](WORKBUDDY-ZOTERO-MCP.zh-CN.md)

## What it provides / 主要功能

- Search Zotero metadata and retrieve full text from local attachments.
- Inspect collections, items, tags, groups, and child attachments.
- Export BibTeX and citation data.
- Build and operate PDF-aware paper-reading queues.
- Run local health checks before connecting WorkBuddy.

- 搜索 Zotero 文献元数据并读取本地附件全文。
- 查看集合、条目、标签、群组和子附件。
- 导出 BibTeX 与引用数据。
- 创建和运行支持 PDF 筛选的论文精读队列。
- 连接 WorkBuddy 前运行本地健康检查。

## Quick start / 快速开始

1. Download [workbuddy-zotero-mcp-portable.zip](workbuddy-zotero-mcp-portable.zip).
2. Extract it on Windows with Python 3.10+ installed.
3. Start Zotero Desktop and enable its Local API.
4. Run `run.ps1 -Check`, then add `server.py` to WorkBuddy's MCP configuration.

1. 下载并解压 [便携包](workbuddy-zotero-mcp-portable.zip)。
2. 在 Windows 中准备 Python 3.10 或更高版本。
3. 启动 Zotero Desktop 并启用 Local API。
4. 运行 `run.ps1 -Check`，再将 `server.py` 加入 WorkBuddy 的 MCP 配置。

## Safety / 安全

The package uses Zotero's local endpoint at `http://127.0.0.1:23119`. Do not commit real API keys, `.env` files, logs, runtime state, or private reading notes.

程序默认使用 `http://127.0.0.1:23119` 的 Zotero 本地接口。不要提交真实 API key、`.env` 文件、日志、运行时状态或私人精读笔记。

Portable package version: 0.1.1.
