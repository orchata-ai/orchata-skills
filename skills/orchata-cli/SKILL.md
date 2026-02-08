---
name: orchata-cli
description: Use Orchata CLI commands to manage knowledge bases from the terminal. For shell/terminal operations only.
metadata:
  version: 1.1.0
  author: Orchata AI
---

# Orchata CLI Commands

Use this skill when you need to run `orchata` commands in a terminal/shell environment.

**Use when:**
- You need to run shell commands in a terminal
- You need to perform batch file uploads
- You're working with file system operations
- You need to script or automate Orchata operations
- You want JSON output for parsing

**Don't use when:**
- You have MCP tools available (use `orchata-mcp` skill instead)
- You need tree-based document navigation (MCP only feature)
- You need programmatic function calls (use `orchata-mcp` skill instead)

---

## What is Orchata?

Orchata is a knowledge management platform that:

- **Organizes documents into Spaces** - Logical containers for related content
- **Provides semantic search** - Find relevant content using natural language queries
- **Provides CLI** - Terminal interface for document management and queries

## Installation & Setup

### Installation

```bash
# Install globally via npm
npm install -g @orchata-ai/cli

# Or via bun
bun add -g @orchata-ai/cli

# Verify installation
orchata --version
```

### Initial Configuration

```bash
# 1. Configure CLI (run once after install)
orchata init

# 2. Authenticate with your API key
orchata login

# 3. Verify connection
orchata spaces list

# 4. (Optional) Set a default space to avoid --space on every command
orchata workspace set
```

### Environment Variables

```bash
# Optional: Override settings via environment variables
export ORCHATA_API_KEY="oai_..."
export ORCHATA_API_BASE="https://api.orchata.ai"
export ORCHATA_PROFILE="production"
```

---

## CLI Commands Reference

### Workspace Context

Set a default space so you don't need `--space` on every command.

#### Set default space (interactive picker)

```bash
orchata workspace set
```

Shows an interactive list of your spaces to choose from.

#### Set default space by ID

```bash
orchata workspace set space_abc123
```

#### Show current default

```bash
orchata workspace show
```

#### Clear default

```bash
orchata workspace clear
```

Once a workspace is set, all commands that normally require `--space` will use the default automatically. You can still override with `--space <id>` on any command.

---

### Space Management

#### List all spaces

```bash
orchata spaces list
```

#### Create a space

```bash
orchata spaces create --name "Product Docs" --description "Technical documentation" --icon book
```

**Options:**
- `--name <name>` - Space name (required)
- `--description <text>` - Space description (optional)
- `--icon <icon>` - Icon: `folder`, `book`, `file-text`, `database`, `package`, `archive`, `briefcase`, `inbox`, `layers`, `box`

#### Get a specific space

```bash
orchata spaces get space_abc123
```

#### Update a space

```bash
orchata spaces update space_abc123 --name "Updated Name" --description "New description"
```

#### Delete a space (soft delete/archive)

```bash
orchata spaces delete space_abc123
```

---

### Document Management

#### List documents in a space

```bash
orchata documents list --space space_abc123

# Or with a default workspace set:
orchata documents list
```

#### Upload a single file

```bash
orchata documents upload ./report.pdf --space space_abc123
orchata documents upload ./guide.md
```

#### Upload multiple files with glob patterns

```bash
# Upload all markdown files in a directory
orchata documents upload docs/*.md --space space_abc123

# Upload with metadata applied to all files
orchata documents upload notes/*.md --metadata '{"tag":"drafts"}'
```

Files are batched automatically (up to 100 per request). A summary is shown:
```
Uploaded 12 file(s)
```

#### Upload inline content

```bash
orchata documents upload --content "# Title\n\nContent here..." --filename my-doc.md
```

#### Batch upload from JSON file

```bash
orchata documents batch --file ./docs.json --space space_abc123
```

#### Upsert (create or update by filename)

```bash
orchata documents upsert --filename "brief.md" --content "# Updated content..."
```

If the filename already exists in the space, it updates the document. If new, it creates it.

#### Get a document

```bash
orchata documents get doc_xyz789 --space space_abc123
```

#### Get document content (processed text)

```bash
orchata documents content doc_xyz789 --space space_abc123
```

#### Append to a document

```bash
orchata documents append doc_xyz789 --content "Additional content..."
```

#### Get by filename

```bash
orchata documents get-by-filename --filename "guide.md"
```

---

### Querying

#### Query with compact output

```bash
orchata query --compact "How do I authenticate?"
```

Output:
```
3 result(s):

api-guide.md - Authentication
  "To authenticate API requests, include your API key in the header..."

handbook.pdf - Security Overview
  "All API calls require authentication via Bearer token..."
```

#### Query with full JSON output

```bash
orchata query "How do I authenticate?" --space space_abc123
```

#### Query with more results

```bash
orchata query "installation guide" --top-k 15
```

**Options:**
- `--space <id>` - Space ID to query (uses workspace default if set, or all spaces)
- `--compact` - Human-readable output instead of JSON
- `--top-k <n>` - Maximum number of results (default: 10)
- `--threshold <n>` - Similarity threshold 0-1 (default: 0)
- `--group-by-space` - Group results by space
- `--metadata <json>` - Metadata filter

#### Smart query (discover relevant spaces)

```bash
orchata query smart "what is orchata"
```

This helps you find which spaces are relevant when you don't know where to look.

---

## Global Options

These options work with any command:

```bash
orchata spaces list --profile production
orchata query "test" --space space_123 --api-key oai_custom_key --json
orchata documents delete doc_123 --yes
```

**Available global options:**
- `--profile <name>` - Use a named profile
- `--api-base <url>` - Override API base URL
- `--app-base <url>` - Override app base URL
- `--api-key <key>` - Override API key for this run
- `--json` - Output raw JSON (useful for scripting)
- `-y, --yes` - Auto-confirm all prompts (non-interactive mode, ideal for agents/scripts)

---

## Common Patterns

### Pattern 1: First-time setup with workspace context

```bash
# 1. Authenticate
orchata login

# 2. Create a space
orchata spaces create --name "Docs" --description "Product documentation"
# Returns: space_abc123

# 3. Set it as your default workspace
orchata workspace set space_abc123

# 4. Now all commands use this space automatically
orchata documents upload ./handbook.pdf
orchata documents upload ./api-guide.md
orchata query --compact "authentication flow"
```

### Pattern 2: Bulk upload with glob patterns

```bash
# Upload all markdown files
orchata documents upload ./documentation/*.md

# Upload with metadata
orchata documents upload ./drafts/*.md --metadata '{"source":"drafts"}'
```

### Pattern 3: Discover and search

```bash
# 1. Find relevant spaces
orchata query smart "billing questions"
# Returns: space_billing (most relevant)

# 2. Query that space
orchata query --compact "payment methods" --space space_billing
```

### Pattern 4: Get raw JSON for scripting

```bash
# Get JSON output for parsing
orchata spaces list --json | jq '.spaces[] | .id'
orchata query "test" --space space_123 --json | jq '.results[0]'
```

### Pattern 5: Non-interactive mode for agents/scripts

```bash
# Auto-confirm all prompts with --yes
orchata workspace set --yes                    # Picks first space
orchata documents delete doc_123 --yes         # No confirmation prompt

# Combine with --json for fully deterministic automation
orchata query "search term" --json --yes
```

### Pattern 6: Profile management for multiple environments

```bash
# Login to different environments
orchata login --profile development
orchata login --profile production

# Use specific profile
orchata query "test" --space space_123 --profile production
```

---

## Document Processing

**Documents are processed asynchronously:**

1. Upload returns immediately with `status="PROCESSING"`
2. Background job generates embeddings and indexes the document (typically 1-3 seconds)
3. Status changes to `"COMPLETED"` when ready
4. Document becomes searchable

**To check completion status:**

```bash
# List all documents with their status
orchata documents list

# Check specific document
orchata documents get doc_xyz789
```

**Supported file formats:**
- PDF (text-based and scanned with OCR)
- Word documents (.docx)
- Excel spreadsheets (.xlsx)
- PowerPoint presentations (.pptx)
- Markdown files (.md)
- Plain text files (.txt)
- Images (PNG, JPG, etc.)

---

## Best Practices

### DO

- **Set a default workspace** - `orchata workspace set` eliminates `--space` on every command
- **Use glob upload for multiple files** - `orchata documents upload docs/*.md`
- **Use `--compact` for quick lookups** - Human-readable query results
- **Use `--json` flag for scripting** - Easy to parse programmatically
- **Use `--yes` for automation** - No interactive prompts in scripts/agents
- **Use `upsert` for iterative docs** - Avoids duplicates when re-uploading
- **Wait 1-3 seconds after upload** - Give documents time to process
- **Check document status before querying** - Only COMPLETED documents are searchable

### DON'T

- **Don't query immediately after upload** - Wait for processing to complete
- **Don't use very short queries** - More context = better results
- **Don't forget to authenticate** - Run `orchata login` first
- **Don't memorize space IDs** - Use `orchata workspace set` for interactive selection

---

## Troubleshooting

### "Command not found: orchata"

**Solution:**
```bash
npm install -g @orchata-ai/cli
orchata --version
```

### "Authentication required"

**Solution:**
```bash
orchata login
# Or set environment variable:
export ORCHATA_API_KEY="oai_..."
```

### "No space specified"

**Solution:**
```bash
# Set a default workspace
orchata workspace set

# Or pass explicitly
orchata documents list --space space_abc123
```

### "Document still processing"

**Solution:**
Wait 1-3 seconds after upload for processing to complete:
```bash
orchata documents list
# Check status field
```

### Upload fails

**Common causes:**
- File is too large (check file size limits)
- Invalid file format (check supported formats above)
- Space is archived (use a different space)
- Network issues (check connection)

---

## Configuration Files

The CLI stores configuration in `~/.orchata/config.json`:

```json
{
  "currentProfile": "cloud",
  "profiles": {
    "cloud": {
      "mode": "cloud",
      "apiBase": "https://api.orchata.ai/api",
      "appBase": "https://app.orchata.ai",
      "apiKey": "oai_...",
      "defaultSpace": "space_abc123"
    },
    "local": {
      "mode": "local",
      "apiBase": "http://localhost:4747/api",
      "appBase": "http://localhost:4747"
    }
  }
}
```

You can also manage configuration with:

```bash
orchata configure --api-base https://api.orchata.ai
orchata configure --profile production --set-default
```

---

## Quick Reference

| Task | Command |
| ---- | ------- |
| **Set default space** | `orchata workspace set` |
| **Show default space** | `orchata workspace show` |
| **List spaces** | `orchata spaces list` |
| **Create space** | `orchata spaces create --name "Docs"` |
| **List documents** | `orchata documents list` |
| **Upload file** | `orchata documents upload ./file.pdf` |
| **Upload glob** | `orchata documents upload docs/*.md` |
| **Upsert document** | `orchata documents upsert --filename "x.md" --content "..."` |
| **Search (compact)** | `orchata query --compact "question"` |
| **Search (JSON)** | `orchata query "question" --json` |
| **Discover spaces** | `orchata query smart "question"` |
| **Non-interactive** | `orchata <command> --yes` |

---

## Script Examples

### Bash: Upload and wait for processing

```bash
#!/bin/bash

SPACE_ID="space_abc123"
FILE="./document.pdf"

# Upload
echo "Uploading ${FILE}..."
RESULT=$(orchata documents upload "${FILE}" --space "${SPACE_ID}" --json)
DOC_ID=$(echo $RESULT | jq -r '.document.id')

echo "Document ID: ${DOC_ID}"
echo "Waiting for processing..."

# Wait for completion
while true; do
  STATUS=$(orchata documents get "${DOC_ID}" --space "${SPACE_ID}" --json | jq -r '.document.status')

  if [ "$STATUS" = "COMPLETED" ]; then
    echo "Processing complete!"
    break
  elif [ "$STATUS" = "FAILED" ]; then
    echo "Processing failed!"
    exit 1
  fi

  echo "Status: ${STATUS}..."
  sleep 2
done

# Query
orchata query --compact "summary of document" --space "${SPACE_ID}"
```

### Bash: Agent-friendly bulk ingest

```bash
#!/bin/bash
# Non-interactive: uses --yes and --json throughout

SPACE_ID="space_abc123"

# Upload all markdown files
orchata documents upload ./docs/*.md --space "${SPACE_ID}" --json --yes

# Wait and verify
sleep 3
orchata documents list --space "${SPACE_ID}" --json | jq '.documents[] | "\(.filename): \(.status)"'
```

---

## Differences from MCP Tools

**CLI does NOT support:**
- Tree-based document navigation (`get_document_tree`, `get_tree_node`)

**CLI excels at:**
- Glob-based batch file uploads (`orchata documents upload docs/*.md`)
- Compact human-readable query output (`--compact`)
- Workspace context persistence (`orchata workspace set`)
- Non-interactive mode for agents (`--yes`)
- Scripting and automation (`--json`)
- Profile management for multiple environments
- File system integration

**For tree-based navigation and programmatic access, use the `orchata-mcp` skill instead.**
