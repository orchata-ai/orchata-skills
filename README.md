# Orchata Skills v1

A collection of skills for AI coding agents to interact with Orchata's knowledge management platform.

## Available Skills

### orchata-mcp

**MCP Tools for programmatic knowledge base access.**

Use this skill for programmatic access to Orchata knowledge bases through MCP server tools.

**Use when:**
* You have MCP tools available (Claude Desktop, MCP-compatible clients)
* You can call functions like `list_spaces`, `query_spaces`, `get_document_tree`
* You need tree-based document navigation
* You need direct document updates and management
* You're integrating Orchata into agent workflows

**Key Capabilities:**
* Space and document management via function calls
* Semantic search with `query_spaces` and `smart_query`
* Tree-based navigation with `get_document_tree` and `get_tree_node`
* Compact query modes for discovery vs. data retrieval
* Direct programmatic access to all features

---

### orchata-cli

**Terminal commands for knowledge base management.**

Use this skill for command-line operations and shell scripting.

**Use when:**
* You need to run shell commands in a terminal
* You need to perform batch file uploads
* You're working with file system operations
* You need JSON output for scripting and automation
* You need to manage multiple environment profiles

**Key Capabilities:**
* Batch directory uploads with `orchata documents batch`
* JSON output mode for scripting (`--json` flag)
* Profile management for dev/staging/production
* File system integration
* Shell scripting and automation
* CI/CD pipeline integration

---

## Installation

```bash
npx skills add orchata-ai/orchata-skills
```

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

### MCP Tools Examples

```
Search for installation instructions in my knowledge base
```

```
Create a new space for technical documentation
```

```
Get the tree structure of the user manual document
```

### CLI Examples

```bash
orchata query "installation instructions" --space space_123
```

```bash
orchata spaces create --name "Technical Docs" --description "Product documentation"
```

```bash
orchata documents batch ./documentation/ --space space_123
```

## Which Skill to Use?

**Use `orchata-mcp` when:**
- ✅ You have MCP server tools available
- ✅ You need tree-based document navigation
- ✅ You need programmatic function calls
- ✅ You need fine-grained control (compact mode, direct updates)

**Use `orchata-cli` when:**
- ✅ You're working in a terminal/shell
- ✅ You need to batch upload directories
- ✅ You need JSON output for scripting
- ✅ You need profile management for multiple environments

**Both access the same Orchata platform - choose based on your environment!**

## Skill Structure

Each skill contains:

* `SKILL.md` - Complete instructions for the agent
* Focused, single-purpose documentation
* Clear examples and workflows
* Best practices and troubleshooting

## About Orchata

Orchata is a knowledge management platform that organizes documents into spaces with semantic search capabilities. It uses tree-based indexing to enable precise navigation of large documents and provides both MCP tools and CLI for AI assistants to manage and query knowledge bases.

**Key Features:**
* Tree-based document indexing
* Semantic search
* Multi-format support (PDF, Word, Excel, PowerPoint, Markdown, images)
* Space-based organization
* Async document processing

## License

MIT
