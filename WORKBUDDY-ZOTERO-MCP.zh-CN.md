# WorkBuddy Zotero MCP

[English documentation](WORKBUDDY-ZOTERO-MCP.md)

这是一个面向 Windows 的可迁移 Zotero 本地 MCP 服务，用于通过 Zotero Local API 和 Connector 接口把 WorkBuddy 接入 Zotero Desktop。

它适合在不同电脑之间迁移基于 Zotero 的论文阅读流程。服务使用 Python 标准库实现，通过 MCP stdio 通信，不依赖 Codex Desktop、Computer Use、截图或鼠标自动化。

## 主要功能

- 检查 Zotero 状态，读取集合、文献库、群组、标签、条目、子附件和本地文件。
- 搜索文献元数据；在存在 PDF 或其他附件时读取全文。
- 导出 BibTeX 和引用数据；执行需要明确确认的 Connector 导入。
- 创建和运行支持 PDF 筛选的精读队列，管理 pending、in-progress、done、failed 状态。
- 重建队列时保留已有状态和尝试次数。
- 可选读取本地 Zotero style JSON 文件，用于期刊标签查询。
- 在连接 WorkBuddy 前运行本地健康检查。

## 使用条件

- Windows
- 已启动的 Zotero Desktop，并启用 Local API
- Python 3.10 或更高版本
- 支持 MCP stdio 服务的 WorkBuddy 版本
- 可选：用于期刊分区或风格查询的本地 Zotero style JSON 文件

Zotero Local API 默认地址为 http://127.0.0.1:23119。程序会绕过系统代理，确保 localhost 请求保持在本机。

## 快速开始

1. 下载并解压 workbuddy-zotero-mcp-portable.zip。
2. 在 PowerShell 中进入解压后的目录。
3. 运行健康检查：

~~~powershell
.\run.ps1 -Check
~~~

4. 将服务加入 WorkBuddy 的 MCP 配置，并把路径改成目标电脑上的实际路径：

~~~json
{
  "mcpServers": {
    "zotero": {
      "command": "py",
      "args": [
        "-3",
        "C:\\path\\to\\workbuddy-zotero-mcp\\server.py"
      ],
      "env": {
        "ZOTERO_LOCAL_BASE_URL": "http://127.0.0.1:23119",
        "ZOTERO_STYLE_FILE": "F:\\documents\\Zotero\\zoterostyle.json",
        "NO_PROXY": "localhost,127.0.0.1"
      }
    }
  }
}
~~~

如果电脑没有 py 启动器，将 command 改为 Python 3.10+ 解释器的绝对路径。

## 压缩包内容

- server.py：MCP stdio 服务入口
- zotero_client.py：Local API、Connector、全文和队列实现
- manifest.json：与平台无关的能力清单示例
- workbuddy-mcp-config.example.json：配置模板
- run.ps1、run.cmd：Windows 启动入口
- tests/：协议和客户端测试
- WORKBUDDY-MIGRATION-PROMPT.md：首次配置和精读流程提示词
- WORKBUDDY-ZOTERO-MCP-DEPLOY-PROMPT.md：交给 WorkBuddy 使用的部署提示词

## MCP 能力

服务提供以下类型的工具：

- 状态与连接检查
- 集合、文献库、条目元数据、子附件、标签和群组
- 文献搜索、全文读取和本地文件 URL
- BibTeX 与引用导出
- 需要明确确认的导入和 Local API 配置
- 精读队列的创建、查看、领取、完成、失败和重置
- 可选的期刊 style 查询

## 精读队列流程

~~~text
WorkBuddy
  -> zotero_collection_items(collectionKey, withPdf=true)
  -> zotero_build_reading_queue
  -> zotero_prepare_next
  -> zotero_get_item / zotero_get_children / zotero_get_fulltext
  -> WorkBuddy 生成 Markdown 精读笔记
  -> zotero_mark_done
~~~

队列可以排除没有 PDF 子附件的顶层文献；重建时保留重试信息，并使用锁目录让多个 worker 安全竞争领取下一篇 pending 文献。

## 迁移说明

请复制完整的解压目录，不要只复制 server.py。目标电脑需要重新确认 Zotero 集合 key、附件路径和 style 文件路径，因为这些信息可能不同。

除非明确需要迁移，否则不要复制 Zotero 数据库、PDF、私人笔记、本地日志或运行时队列状态。

## 安全边界

- Local API 的读取不需要 Zotero Web API key。
- Connector 导入和偏好设置修改必须显式确认。
- 不要提交真实 API key、.env 文件、本地日志、运行时状态或私人精读笔记。
- manifest.json 是通用示例，不是 WorkBuddy 官方 manifest；实际导入时请遵循 WorkBuddy 当前 MCP 配置格式。

压缩包内部的 README.md、NOTICE.md 以及两个迁移/部署提示词还包含更具体的说明。

## 版本

当前便携包版本：0.1.1。
