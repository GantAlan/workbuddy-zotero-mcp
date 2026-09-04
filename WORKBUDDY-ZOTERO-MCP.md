# WorkBuddy Zotero MCP

[中文说明](WORKBUDDY-ZOTERO-MCP.zh-CN.md)

A portable, Windows-friendly MCP server that connects WorkBuddy to Zotero Desktop through Zotero's local API and Connector interfaces.

This package is designed for moving a Zotero-powered paper-reading workflow between computers. It uses Python's standard library only, communicates over MCP stdio, and does not depend on Codex Desktop, Computer Use, screenshots, or mouse automation.

## Highlights

- Inspect Zotero status, collections, libraries, groups, tags, items, child attachments, and local files.
- Search metadata and retrieve full text when a PDF or other attachment is available.
- Export BibTeX and citation data, and perform explicitly confirmed Connector imports.
- Build and operate PDF-aware reading queues with pending, in-progress, done, and failed states.
- Preserve queue state and retry counts during a rebuild.
- Optionally read journal-style information from a local Zotero style file.
- Run a local health check before connecting WorkBuddy.

## Requirements

- Windows
- Zotero Desktop with the Local API enabled and Zotero running
- Python 3.10 or newer
- A WorkBuddy version that supports MCP stdio servers
- Optional: a local Zotero style JSON file for journal-label queries

The default Zotero Local API endpoint is http://127.0.0.1:23119. Local requests bypass system proxies so that localhost traffic remains local.

## Quick start

1. Download and extract workbuddy-zotero-mcp-portable.zip.
2. Open the extracted folder in PowerShell.
3. Run the health check:

~~~powershell
.\run.ps1 -Check
~~~

4. Add the server to WorkBuddy's MCP configuration. Update every path for the target computer:

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

If the py launcher is unavailable, replace command with the absolute path to a Python 3.10+ executable.

## Included files

- server.py: MCP stdio entry point
- zotero_client.py: Local API, Connector, full-text, and queue implementation
- manifest.json: vendor-neutral capability manifest example
- workbuddy-mcp-config.example.json: configuration template
- run.ps1 and run.cmd: Windows launchers
- tests/: protocol and client tests
- WORKBUDDY-MIGRATION-PROMPT.md: first-run migration and reading workflow prompt
- WORKBUDDY-ZOTERO-MCP-DEPLOY-PROMPT.md: deployment prompt for WorkBuddy

## MCP capabilities

The server exposes tools for:

- Status and connectivity checks
- Collections, inventory, item metadata, child attachments, tags, and groups
- Search, full-text retrieval, and local file URLs
- BibTeX and citation export
- Explicitly confirmed imports and Local API configuration
- Reading-queue creation, inspection, item preparation, completion, failure, and reset
- Optional journal-style lookup

## Reading-queue workflow

~~~text
WorkBuddy
  -> zotero_collection_items(collectionKey, withPdf=true)
  -> zotero_build_reading_queue
  -> zotero_prepare_next
  -> zotero_get_item / zotero_get_children / zotero_get_fulltext
  -> WorkBuddy generates a Markdown reading note
  -> zotero_mark_done
~~~

Queues can exclude top-level items without PDF attachments, keep retry information during rebuilds, and use a lock directory so multiple workers can safely compete for the next pending paper.

## Migration notes

Copy the complete extracted folder to the target computer. Do not copy a Zotero database, PDFs, private notes, logs, or queue state unless you intentionally want to migrate them. Collection keys, attachment paths, and style-file paths may differ between computers; discover them again with the status and collection tools.

## Safety

- Local API reads do not require a Zotero Web API key.
- Connector imports and preference changes require explicit confirmation.
- Do not commit real API keys, .env files, local logs, runtime state, or private reading notes.
- manifest.json is a vendor-neutral example, not an official WorkBuddy manifest. Follow WorkBuddy's current MCP configuration schema.

See README.md, NOTICE.md, and the two migration/deployment prompts inside the archive for package-level details.

## Version

Portable package version: 0.1.1.
