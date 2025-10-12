# In-Depth Example: Using MCP with Gmail 📩 

Now that we have looked at a few use cases and a high-level overview of MCP, let's dive into a **detailed, technical example** showing how an LLM communicates with Gmail via MCP.

This example demonstrates how MCP works **end-to-end**, including how an LLM translates a user request into an actionable request for Gmail, and how the server executes it.

---

## A quick note on JSON-RPC

Most of agentic communications take place using the communications protocol JSON RPC; It is just a way of formatting requests and responses.

---

## Scenario

User says:

> “Send an email to Alice with the report attached.”

!!! example "LLM connects to Gmail tool using MCP"
    Email gets sent.

User gets back a response:

> "Done! I have sent the email to Alice using your gmail account."

---

## End to end journey of the request

We want to show **everything that happens behind the scenes**:

??? "0. **Tool Discovery (Handshake)**"
    - Before doing anything, the **MCP Host** (LLM) asks the **MCP Client** what tools are available.  
    - The Client calls `get_capabilities` on the **MCP Server**.  
    - The Server responds with a list of tools (e.g. `gmail.send_email`, `calendar.create_event`) and their input schemas.  
    - The Host stores this so it knows *what’s possible* before interpreting user intent.  

??? "1. **Intent Parsing (MCP Host / LLM)**"
    - LLM interprets natural language query.  
    - Identifies **action** (`send_email`) and **parameters** (`to`, `subject`, `body`).  
    - Produces a **structured intermediate representation** for MCP Client.

??? "2. **MCP Client Translation**"
    - Converts LLM intent into **MCP JSON-RPC format**.  
    - Handles data validation (email addresses, attachment size).  
    - Ensures it conforms to MCP standard (tool name, method, params).  

??? "3. **MCP Server Execution**"
    - Receives JSON-RPC request.  
    - Maps MCP method to **Gmail API endpoints**.  
        - e.g., `send_email` → `POST https://gmail.googleapis.com/gmail/v1/users/me/messages/send`  
    - Handles authentication / OAuth tokens.  
    - Formats body and attachments according to Gmail API.  
    - Sends request, receives HTTP response, converts it back into MCP response format.  

??? "4. **MCP Response Handling**"
    - MCP Client receives server response.  
    - Converts it into a structured result understandable by the LLM.  
    - LLM generates a human-readable confirmation for the user.

---

## Detailed Data Flow

```mermaid
sequenceDiagram
  participant U as User
  participant H as MCP Host (LLM)
  participant C as MCP Client
  participant S as MCP Server
  participant G as Gmail API
  U->>H: "Send an email to Alice with report attached"
  H->>C: Ask available tools (get_capabilities)
  C->>S: Request list of tools
  S-->>C: Returns available tools (e.g. gmail.send_email)
  C-->>H: Tool metadata returned
  H->>H: Parse intent → identify action & parameters
  H->>C: MCP request JSON (tool: gmail, method: send_email, params {...})
  C->>S: Send JSON-RPC request
  S->>G: Map MCP → Gmail API call (POST /messages/send)
  G-->>S: Gmail HTTP Response (200 OK / error)
  S->>C: Convert Gmail response → MCP JSON-RPC response
  C->>H: Return structured result
  H->>U: "Email sent successfully."
```

---

## Example MCP Request (JSON-RPC)

??? "MCP Request (JSON-RPC)"
    ```json
    {
      "jsonrpc": "2.0",
      "id": "req_001",
      "method": "send_email",
      "tool": "gmail",
      "params": {
        "to": "alice@example.com",
        "subject": "Monthly Report",
        "body": "Hi Alice, see attached.",
        "attachments": [
          {
            "filename": "report.pdf",
            "content_base64": "JVBERi0xLjQKJc..."
          }
        ]
      }
    }
    ```

Key points:

- `jsonrpc: "2.0"` → standard JSON-RPC 2.0 format.
- `id` → unique request ID for matching response.
- Attachments encoded in Base64.
- MCP Client ensures all fields conform to tool schema.

## 🧰 MCP Server → Gmail API Call

**HTTP POST Request** (pseudo-code):

??? example "Gmail API Call (POST)"
    ```http
    POST /gmail/v1/users/me/messages/send
    Authorization: Bearer <OAuthToken>
    Content-Type: application/json

    {
      "raw": "<Base64 encoded MIME email including attachment>"
    }
    ```

- MCP Server handles **translation from MCP JSON → Gmail API format**.  
- OAuth token ensures proper authentication.  
- Handles Gmail-specific errors (invalid recipient, attachment too large).

---

## 🔄 Example MCP Response

??? example "Success Response"
    ```json
    {
      "jsonrpc": "2.0",
      "id": "req_001",
      "result": {
        "status": "success",
        "message": "Email sent successfully.",
        "emailId": "1789abcdef"
      }
    }
    ```

??? example "Error Response"
    ```json
    {
      "jsonrpc": "2.0",
      "id": "req_001",
      "error": {
        "code": 400,
        "message": "Attachment exceeds size limit"
      }
    }
    ```

---

## 🔍 Summary Table (Technical)

| Component | Role | Technical Detail |
|------------|------|----------------|
| **MCP Host (LLM)** | Understands user intent | Parses natural language → structured action/params |
| **MCP Client** | Translates & validates requests | JSON-RPC, schema validation, Base64 attachments |
| **MCP Server** | Executes tool-specific calls | Maps MCP → Gmail API, handles auth & errors |
| **Gmail API** | Actual tool | REST API endpoint `POST /messages/send` |
| **Communication Standard** | MCP Protocol | JSON-RPC 2.0 between Host/Client/Server |
