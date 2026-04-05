# 🧠 News Agent MCP Server

## 1. Introduction

The **News Agent MCP Server** is a lightweight, schema-backed capability engine designed for LLM-based agents. It provides structured and semi-structured information retrieval tools over a streamable HTTP interface, enabling agents to perform iterative research workflows.

This server is **not intended to be exposed as a public API**. Instead, it operates as an internal execution layer within a larger system, where orchestration, safety, and control are handled externally.

---

## 2. System Role in Architecture

This MCP server is part of a modular LLM system with clear separation of responsibilities:

- **Agent (LLM Layer)**  
  Handles reasoning, decision-making, and tool selection

- **MCP Server (This Repository)**  
  Executes capabilities such as news retrieval and web scraping

- **Gateway Layer (Go-based)**  
  Manages rate limiting, authentication, routing, and safety constraints

This separation ensures:
- Cleaner system design  
- Easier scalability  
- Independent evolution of components  

---

## 3. Design Philosophy

The server is intentionally designed with the following principles:

- **Minimal Core Engine**  
  Focus only on capability execution, not orchestration or control

- **Schema Where Needed, Flexibility Where Needed**  
  - Structured outputs for predictable data (news)
  - Flexible outputs for rich content (web scraping)

- **LLM-First Interface**  
  All inputs and outputs are strings to ensure seamless integration with LLM agents

- **Externalized Concerns**  
  Features like rate limiting, authentication, and recursion control are handled outside this service

---

## 4. Architecture Overview

- Built using **FastMCP (Python)**
- Tools are exposed via `@tool` decorators
- Runs as a **streamable HTTP server**
- Endpoint exposed at: `/mcp`
- Designed to be deployed as a **Docker container**

---

## 5. Capabilities

### 5.1 `get_latest_news` — Discovery Layer

Fetches recent news articles based on a given topic.

**Input**
- `topic` (string)

**Output**
- JSON string (serialized)

Internally backed by a Pydantic model:

```python
class Article(BaseModel):
    article_id: Optional[str] = None
    link: Optional[str] = None
    title: Optional[str] = None
    description: Optional[str] = None
    creator: Optional[list[str]] = None
    country: Optional[list[str]] = None
    category: Optional[list[str]] = None
    pubDate: Optional[str] = None
    image_url: Optional[str] = None
    source_url: Optional[str] = None
```

External Dependency:
- newsdata.io

Role:
- Acts as the entry point for discovery
- Provides structured, machine-consumable data for agents
