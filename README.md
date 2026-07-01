# ⚡ FastMCP Python REPL Server

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue?style=for-the-badge&logo=python)](https://python.org)
[![FastMCP](https://img.shields.io/badge/FastMCP-Latest-green?style=for-the-badge)](https://github.com/jlowin/fastmcp)
[![LLM](https://img.shields.io/badge/LLM-Compatible-purple?style=for-the-badge)](https://openai.com)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

> 🤖 A powerful Python REPL server built with **FastMCP** — enabling LLMs (Claude, GPT-4, etc.) to execute Python code in real-time!

---

## 🚀 What is this?

This is an **MCP (Model Context Protocol) server** that exposes a Python REPL as a tool. This means:
- 🧠 Your AI (Claude, GPT-4, etc.) can **write AND run Python code**
- 📊 Perfect for data analysis, automation, and complex calculations
- ⚡ Built on FastMCP for blazing fast performance

---

## ✨ Features

- 🐍 Full Python REPL execution environment
- 🔧 MCP-compatible (works with Claude, OpenAI, any LLM)
- 📦 Easy to install and run
- 🌐 HTTP server with SSE support

> ⚠️ **Note on security:** This server executes arbitrary Python code with no built-in authentication or sandboxing beyond the host process. Do not expose this publicly without adding an API key / auth layer and running it inside an isolated container — anyone who can reach the endpoint can run code with the server's own privileges.

---

## 📦 Installation

```bash
git clone https://github.com/arbaz-builds/fastmcp-python-repl-server.git
cd fastmcp-python-repl-server
pip install -r requirements.txt
```

## 🏃 Usage

```bash
python main.py
```

Then connect your LLM client to the MCP server endpoint (default: `http://localhost:10000/mcp`, or your deployed URL + `/mcp`).

---

## 🛠️ Tech Stack

- **FastMCP** — MCP server framework
- **Python** — REPL execution
- **SSE** — Real-time streaming

---

## 🌟 Star this repo if it helped you!

[![GitHub stars](https://img.shields.io/github/stars/arbaz-builds/fastmcp-python-repl-server?style=social)](https://github.com/arbaz-builds/fastmcp-python-repl-server/stargazers)

---

## 👨‍💻 Author

**Arbaz** — AI/ML Developer
- GitHub: [@arbaz-builds](https://github.com/arbaz-builds)
