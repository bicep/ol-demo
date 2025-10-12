---
marp: true
theme: default
paginate: true
---
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>


<style>
p > img { 
    display: block;           /* 🟨 Makes the image take its own line */
    margin-left: auto;        /* 🟨 Push image to center horizontally */
    margin-right: auto;       /* 🟨 Push image to center horizontally */
}
.mermaid {
    display: flex;              /* 🟨 Enable flexbox */
    justify-content: center;    /* 🟨 Center horizontally */
    align-items: center;        /* 🟨 Center vertically (optional) */
}
</style>


# How Does AI Talk to Its Tools?

> Explaining the Model Context Protocol (MCP) simply

---

| ![image-1](image-1.png) | ![image-3](image-3.png) |
|-------------------------|-------------------------|

---

![alt text](image.png)

---

## 🎯 The Core Idea
If you forget everything else, remember this:

> **Think of MCP as a universal docking station for AI models. It lets LLMs connect to any tool — Gmail, Slack, Figma, you name it — through a single, standardized protocol.**

---

# Understanding MCP

> MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems. Using MCP, AI applications like Claude or ChatGPT can connect to data sources (e.g. local files, databases), tools (e.g. search engines, calculators) and workflows (e.g. specialized prompts)—enabling them to access key information and perform tasks.

---

## 1️⃣ The Problem MCP is trying to Solve
Modern AI tools often act in isolation — each with their own APIs and context limits.  
This leads to duplication, inconsistency, and limited cooperation between systems.

<div class="mermaid">
sequenceDiagram
    participant Model
    participant ToolA
    participant ToolB
    Model->>ToolA: Custom API call
    Model->>ToolB: Custom API call
</div>

Without a standard, every connection is a separate cable — fragile and error-prone.

---

## 2️⃣ The Core Concept

At its heart, MCP defines a shared protocol for how large language models communicate with its tools

<div class="mermaid">
flowchart TD
    A[AI Model] -->|Request| B[MCP Server]
    B -->|Invoke| C[Tool 1]
    B -->|Invoke| D[Tool 2]
    C --> B
    D --> B
    B -->|Return| A
</div>

> The MCP server acts as a universal connector, handling requests and responses in a standard format.

---

## 3️⃣ MCP Architecture: Client & Server

> MCP has three main components: **Host (AI model)**, **Client**, and **Server (Tool)**.  

---

| ![MCP Architecture](image-5.png)| 
|:--:| 
| *MCP Architecture (Image: [Kashish Hora](https://mcpcat.io/blog/mcp-server-client-host/))* |

---

<div class="mermaid">
flowchart LR
    A[AI Model / Host] -->|Sends Intent| B[MCP Client]
    B -->|Standardized MCP Request| C[MCP Server]
    C -->|Executes Action| D[External Tool]
    D --> C
    C -->|Standardized Response| B
    B -->|Delivers Result| A
</div>

- **Host / Model**: Knows what it wants to do (e.g., send an email).  
- **Client**: Converts intent into an MCP request the server can understand.  
- **Server**: Executes the request on the tool and sends back a standard response.  

---

## 4️⃣ Concrete Example: Sending an Email

> Imagine your AI wants to send an email via Gmail.

<div class="mermaid">
sequenceDiagram
    participant User
    participant Host
    participant Client
    participant Server
    participant Gmail
    User->>Host: "Send email to Alice with report"
    Host->>Client: Generate MCP request
    Client->>Server: send_email request (to: Alice, body: report)
    Server->>Gmail: Execute send email
    Gmail-->>Server: Email sent
    Server->>Client: Success response
    Client->>Host: Return structured result
    Host->>User: Email sent confirmation
</div>


- The AI never talks directly to Gmail — it goes through **MCP Client & Server**.  
- Any tool (Slack, Google Docs, etc.) can be plugged in using the same pattern.  

---

## 5️⃣ Why This Matters: Product Use Cases


| Use Case | How MCP Applies | MCP Components Involved |
|----------|----------------|------------------------|
| **📩 Automating Email** | AI sends, schedules, or drafts emails through Gmail | Host (LLM) → Client → Gmail Server |
| **📊 Generating Reports** | AI pulls data from multiple sources (Excel, Google Sheets, databases) and formats reports | Host → Client → Tool Servers (Sheets, DBs) |
| **💬 Managing Chatbots** | AI coordinates multi-platform chatbots (Slack, WhatsApp, internal tools) seamlessly | Host → Client → Chat Platforms Servers |

> We’ll go into detail on [**email automation**](example.md), which demonstrates the full MCP flow in a simple example.
