# Orchata Skills - Agent Configuration

This repository contains two separate skills for interacting with Orchata's knowledge management platform.

## Available Skills

### orchata-mcp (MCP Tools)

**Load this skill when:**
- You have access to MCP server tools
- You can call functions like `list_spaces`, `query_spaces`, etc.
- User asks about searching or managing knowledge bases
- User needs tree-based document navigation
- User mentions Orchata and you have MCP tools available

**Skill file:** `skills/orchata-mcp/SKILL.md`

**Don't load when:**
- You don't have MCP tools available
- User needs terminal/CLI operations
- User needs batch file uploads

---

### orchata-cli (Terminal Commands)

**Load this skill when:**
- You need to run `orchata` commands in a terminal
- User asks to upload files or directories
- User needs batch operations
- User mentions shell scripts or automation
- User asks about the `orchata` CLI

**Skill file:** `skills/orchata-cli/SKILL.md`

**Don't load when:**
- You have MCP tools available and don't need terminal operations
- User needs tree-based document navigation (MCP only)
- User doesn't need shell/terminal access

---

## Decision Tree

```
Does the user need Orchata operations?
│
├─ Yes, and I have MCP tools available
│  └─ Load: skills/orchata-mcp/SKILL.md
│
├─ Yes, and I need terminal/CLI operations
│  └─ Load: skills/orchata-cli/SKILL.md
│
└─ No
   └─ Don't load either skill
```

## Usage Patterns

### MCP Tools (orchata-mcp)

**Syntax:**
```text
list_spaces with status="active"
query_spaces with query="test" spaceIds="space_123"
get_document_tree with spaceId="space_123" documentId="doc_456"
```

**Best for:**
- Programmatic knowledge base access
- Tree-based document navigation
- Direct updates and management
- Integration into agent workflows
- Fine-grained control (compact mode, etc.)

---

### CLI Commands (orchata-cli)

**Syntax:**
```bash
orchata spaces list
orchata query "test" --space space_123
orchata documents batch ./directory/ --space space_123
```

**Best for:**
- Terminal operations
- Batch file uploads
- Shell scripting and automation
- JSON output for parsing
- Profile management (dev/staging/production)

---

## 🚨 CRITICAL: Do Not Mix Skills

**These are separate skills with different interfaces:**

❌ **DON'T:**
- Load both skills at once
- Mix MCP syntax with CLI syntax
- Use MCP tool names with CLI commands
- Use CLI flags with MCP tools

✅ **DO:**
- Choose ONE skill based on your environment
- Use the correct syntax for that skill
- Refer to the skill file for exact command format

---

## Workflow Recommendations

### For Search Tasks

**MCP:**
```text
query_spaces with query="your question"
```

**CLI:**
```bash
orchata query "your question" --space space_id
```

Start with a single query - don't over-complicate.

### For Document Upload

**MCP:**
```text
manage_space with action="create" name="Docs"
save_document with spaceId="space_123" filename="file.md" content="..."
list_documents with spaceId="space_123" status="COMPLETED"
```

**CLI:**
```bash
orchata spaces create --name "Docs"
orchata documents upload ./file.pdf --space space_123
# Or batch:
orchata documents batch ./directory/ --space space_123
orchata documents list --space space_123
```

### For Large Document Navigation

**MCP only** (not available in CLI):
```text
get_document_tree with spaceId="space_123" documentId="doc_456"
get_tree_node with documentId="doc_456" nodeId="0001"
```

---

## Best Practices

### Interface Selection

- **Check your environment first** - Do you have MCP tools or terminal access?
- **Choose one skill** - Don't load both simultaneously
- **Verify syntax** - Refer to the skill file, don't guess

### Query Optimization

- **Start with single queries** - Avoid multi-step workflows
- **Use descriptive queries** - Natural language works best
- **Check document status** - Only COMPLETED documents are searchable
- **Wait after upload** - Documents need 1-3 seconds to process

### Error Handling

Both skills document common errors and solutions:
- Space not found
- Document still processing
- Authentication errors
- Invalid parameters

Refer to the specific skill file for troubleshooting guidance.

---

## When to Load Which Skill

| User Request | Load This Skill |
| ------------ | --------------- |
| "Search my knowledge base" | orchata-mcp (if MCP available) or orchata-cli |
| "Upload these files" | orchata-cli (better for batch) |
| "Show me the document structure" | orchata-mcp (tree navigation) |
| "Run this orchata command" | orchata-cli |
| "Query this space" | orchata-mcp (if MCP available) or orchata-cli |
| "Batch upload this directory" | orchata-cli (best option) |
| "Get document tree" | orchata-mcp (only option) |

---

## MCP Tool Integration

The `orchata-mcp` skill assumes these MCP tools are available:
- `list_spaces`
- `manage_space`
- `list_documents`
- `save_document`
- `get_document`
- `update_document`
- `delete_document`
- `query_spaces`
- `smart_query`
- `get_document_tree`
- `get_tree_node`

If these tools are not available, use the `orchata-cli` skill instead.

---

## CLI Integration

The `orchata-cli` skill requires:
- `orchata` CLI installed (`npm install -g @orchata/cli`)
- Terminal/shell access
- Authentication configured (`orchata login`)

If CLI is not installed, use the `orchata-mcp` skill instead.
