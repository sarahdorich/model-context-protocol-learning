# MCP Chat

MCP Chat is a command-line interface application that enables interactive chat capabilities with AI models through the Anthropic API. The application supports document retrieval, command-based prompts, and extensible tool integrations via the MCP (Model Control Protocol) architecture.

## Prerequisites

- Python 3.9+
- Anthropic API Key

## Setup

### Step 1: Configure the environment variables

1. Create or edit the `.env` file in the project root and verify that the following variables are set correctly:

```
ANTHROPIC_API_KEY=""  # Enter your Anthropic API secret key
```

### Step 2: Install dependencies

#### Option 1: Setup with uv (Recommended)

[uv](https://github.com/astral-sh/uv) is a fast Python package installer and resolver.

1. Install uv, if not already installed:

```bash
pip install uv
```

2. Create and activate a virtual environment:

```bash
uv venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies:

```bash
uv pip install -e .
```

4. Run the project

```bash
uv run main.py
```

#### Option 2: Setup without uv

1. Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

2. Install dependencies:

```bash
pip install anthropic python-dotenv prompt-toolkit "mcp[cli]==1.8.0"
```

3. Run the project

```bash
python main.py
```

## Usage

### Basic Interaction

Simply type your message and press Enter to chat with the model.

### Document Retrieval

Use the @ symbol followed by a document ID to include document content in your query:

```
> Tell me about @deposition.md
```

### Commands

Use the / prefix to execute commands defined in the MCP server:

```
> /summarize deposition.md
```

Commands will auto-complete when you press Tab.

## Development

### Testing Tools

Start the MCP Server:
```bash
mcp dev mcp_server.py
```

Then navigate to localhost:6277 in a browser to access the MCP Inspector tool.

### Adding New Documents

Edit the `mcp_server.py` file to add new documents to the `docs` dictionary.

### Implementing MCP Features

To fully implement the MCP features:

1. Complete the TODOs in `mcp_server.py`
2. Implement the missing functionality in `mcp_client.py`

### Linting and Typing Check

There are no lint or type checks implemented.

## Notes from course

A MCP Client is what allows us to access the functionality that's implemented in the MCP Server.

### The benefits

MCP (Model Context Protocol) servers are designed specifically for AI agent interactions, offering dynamic tool discovery, stateful context management, and standardized communication, which provides significant advantages over simply building rigid, hard-coded API calls to an LLM like Claude. Traditional APIs are generally stateless and built for human developers to integrate applications, while MCP enables more autonomous and flexible AI workflows.

- Dynamic Tool Discovery: AI agents can find available tools and their descriptions by querying an MCP server. This allows them to adapt to new capabilities. Developers do not need to write custom integration code for each function or update hardcoded endpoints.
- Stateful Context Management: MCP maintains session-level context, unlike stateless APIs. This allows AI agents to engage in ongoing, multi-step "conversations" and workflows. Agents can build a richer understanding of a task across multiple interactions.
- AI-Friendly Format: MCP servers provide information, including error messages and tool descriptions, in a structured, AI-friendly format. The language model can easily interpret this and act upon it. This reduces hallucinations and the need for complex prompt engineering.
- Enhanced Agent Autonomy: MCP is built for AI agents, allowing them to iteratively call tools, analyze results, and decide on the next steps in a loop. This enables more complex and dynamic agentic behavior.
Standardization: MCP is an open standard. Tools built on the protocol can be used across any MCP-compatible LLM client (e.g., Claude, GPT, Gemini). This avoids vendor lock-in and promotes consistency across different AI platforms.
- Improved Security Model: The AI model never directly sees API keys or sensitive URLs in an MCP setup. The MCP server acts as a controlled intermediary. Developers can implement security, validation, and monitoring over what the agent can access or do.

### MCP Server Primitives

1. Tools: model-controlled - Claude decides when to call these - results are used by Claude.
2. Resources: app-controlled - our app decides when to call these - results are primarily used by our app.
3. Prompts: user-controlled - the user decides when to use these

### Tools versus Resources

**Tools** are for actions — the LLM decides to call them with parameters it chooses. They can have side effects (e.g. editing a document, calling an API). Think of them like function calls where the model is taking an action.

**Resources** are for data access — identified by a URI, meant to be read-only. The client (not the LLM) typically fetches these to attach context before a conversation starts.

| Situation | Use |
|---|---|
| LLM needs to *do* something (search, write, call an API) | Tool |
| LLM needs information it can *act on* | Tool |
| Client wants to pre-load context into a conversation | Resource |
| You're exposing a catalog or static data structure | Resource |

### Prompts

If you always leave prompts totally to the user, they might get back decent results but user might have more luck if they use our thoroughly-eval'd prompt instead!

Adding prompts to your MCP Server will expose these to any client using the server. 


