# FastMCP Python REPL Server

[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)](https://python.org)
[![FastMCP](https://img.shields.io/badge/FastMCP-2.x-green)](https://github.com/jlowin/fastmcp)
[![Protocol](https://img.shields.io/badge/MCP-Streamable%20HTTP-purple)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

A minimal [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server that gives any MCP-compatible AI assistant (Claude, GPT, etc.) a live Python REPL as a callable tool.

### 🚀 Live MCP Endpoint

```
https://fastmcp-python-repl-server-production-9640.up.railway.app/mcp
```

---

## What it does

The server exposes exactly one MCP tool, `run_python`, backed by LangChain's `PythonREPLTool`:

- The assistant sends a string of Python code.
- The server executes it in a persistent REPL session (variables and imports carry over between calls).
- stdout/stderr is captured and returned as the tool result.

This is useful for letting an LLM do actual computation, quick data manipulation, or verification of its own logic instead of just reasoning in text.

## How it's built

- **[FastMCP](https://github.com/jlowin/fastmcp)** — handles MCP protocol details, tool registration (`@mcp.tool()`), and the HTTP transport.
- **`langchain_experimental.tools.python.tool.PythonREPLTool`** — the actual code execution engine; one shared instance per server process, so state persists across calls.
- **Transport:** `streamable-http`, served at the `/mcp` path, listening on `0.0.0.0` and the platform-provided `$PORT` (defaults to `10000` locally).

## Architecture

```
MCP Client (Claude, LangChain, etc.)
            │
            ▼
     FastMCP Server  (streamable-http, /mcp)
            │
            ▼
      run_python Tool
            │
            ▼
  LangChain PythonREPLTool
            │
            ▼
    Python Interpreter
            │
            ▼
          Output
```

**Example flow:** Claude decides it needs to compute something → calls `run_python()` → FastMCP routes the call → `PythonREPLTool` executes the code → result flows back through FastMCP → Claude receives the output and continues reasoning.

## Compatible clients

- ✅ Claude Desktop (via MCP config)
- ✅ `langchain-mcp-adapters` (`MultiServerMCPClient`)
- ✅ Any custom MCP client speaking `streamable-http`
- ✅ OpenAI Agents SDK (MCP support)

## Current limitations

- **Single shared REPL** — one global session for all callers; no per-client isolation
- **No sandboxing** — code runs with the host process's own permissions
- **No authentication** — the `/mcp` endpoint is open to anyone who can reach it
- **No execution timeout** — a long-running or infinite loop will block the shared REPL for all callers

## Installation

```bash
git clone https://github.com/arbaz-builds/fastmcp-python-repl-server.git
cd fastmcp-python-repl-server
pip install -r requirements.txt
```

## Running locally

```bash
python main.py
```

The server starts at `http://localhost:10000/mcp`. Point any MCP client (Claude Desktop, a custom `langchain-mcp-adapters` client, etc.) at that URL.

## Deploying

Any platform that can run a long-lived Python process and exposes a `$PORT` env var works out of the box (Render, Railway, Fly.io, etc.) — no extra configuration needed beyond installing `requirements.txt` and starting `python main.py`.

## Tool reference

### `run_python(code: str) -> str`

Executes `code` in the shared REPL session and returns its captured output.

```python
# input
"print(sum([1, 2, 3]))"

# output
"6"
```

**Example MCP request/response:**

```
Client call:
  run_python("import math\nprint(math.factorial(5))")

↓

Server output:
  "120"
```

## ⚠️ Security

This server executes **arbitrary Python code with no authentication or sandboxing** beyond the host process. Anyone who can reach the `/mcp` endpoint can run code with the server's own privileges (including network/file access from that machine).

Do not expose this publicly as-is. Before any real deployment, add at minimum:
- An auth layer (API key / bearer token check in front of the MCP endpoint)
- Process isolation (container with restricted permissions, no sensitive mounted volumes or credentials)
- Resource limits (timeouts, memory caps) to prevent runaway or malicious code from affecting the host

## Tech stack

| Component | Purpose |
|---|---|
| FastMCP | MCP server framework, tool registration, HTTP transport |
| langchain-experimental | `PythonREPLTool` — the code execution engine |
| uvicorn | ASGI server underneath FastMCP's HTTP transport |

## Author

**Mohammad Arbaz** — AI/LLM Engineer
[GitHub @arbaz-builds](https://github.com/arbaz-builds)
