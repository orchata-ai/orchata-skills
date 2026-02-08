---
name: orchata-cli
description: Use Orchata CLI commands to manage knowledge bases from the terminal. For shell/terminal operations only.
metadata:
  version: 1.0.0
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
npm install -g @orchata/cli

# Or via bun
bun add -g @orchata/cli

# Or via pnpm
pnpm add -g @orchata/cli

# Or via yarn
yarn global add @orchata/cli

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
```

#### Upload a file

```bash
orchata documents upload ./path/to/file.pdf --space space_abc123
orchata documents upload ./guide.md --space space_abc123
```

#### Upload inline content

```bash
orchata documents upload --space space_abc123 --content "# Title\n\nContent here..."
```

#### Batch upload (directory)

```bash
orchata documents batch ./docs/ --space space_abc123
```

**This is the most powerful CLI feature** - upload entire directories in one command!

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
orchata documents append doc_xyz789 --space space_abc123 --content "Additional content..."
```

---

### Querying

#### Query a specific space

```bash
orchata query "How do I authenticate?" --space space_abc123
```

#### Query with more results

```bash
orchata query "installation guide" --space space_abc123 --top-k 15
```

**Options:**
- `--space <id>` - Space ID to query (required for basic query)
- `--top-k <n>` - Maximum number of results (default: 10)

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
```

**Available global options:**
- `--profile <name>` - Use a named profile
- `--api-base <url>` - Override API base URL
- `--app-base <url>` - Override app base URL
- `--api-key <key>` - Override API key for this run
- `--json` - Output raw JSON (useful for scripting)

---

## Common Patterns

### Pattern 1: Upload files and query them

```bash
# 1. Create a space
orchata spaces create --name "Docs" --description "Product documentation"
# Returns: space_abc123

# 2. Upload documents
orchata documents upload ./handbook.pdf --space space_abc123
orchata documents upload ./api-guide.md --space space_abc123

# 3. Wait ~1-3 seconds for processing, then query
orchata query "authentication flow" --space space_abc123
```

### Pattern 2: Batch upload directory

```bash
# Upload all files in a directory
orchata documents batch ./documentation/ --space space_abc123
```

**This is ideal for:**
- Initial knowledge base setup
- Bulk document imports
- Directory synchronization

### Pattern 3: Discover and search

```bash
# 1. Find relevant spaces
orchata query smart "billing questions"
# Returns: space_billing (most relevant)

# 2. Query that space
orchata query "payment methods" --space space_billing
```

### Pattern 4: Get raw JSON for scripting

```bash
# Get JSON output for parsing
orchata spaces list --json | jq '.spaces[] | .id'
orchata query "test" --space space_123 --json | jq '.results[0].content'
```

**Use JSON mode for:**
- Shell scripts
- CI/CD pipelines
- Automation workflows
- Data processing

### Pattern 5: Profile management for multiple environments

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
orchata documents list --space space_abc123

# Check specific document
orchata documents get doc_xyz789 --space space_abc123
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

- **Use batch upload for directories** - `orchata documents batch` is efficient
- **Use `--json` flag for scripting** - Easy to parse programmatically
- **Wait 1-3 seconds after upload** - Give documents time to process
- **Use descriptive space names and descriptions** - Helps with discovery
- **Check document status before querying** - Only COMPLETED documents are searchable
- **Use profiles for multiple environments** - dev, staging, production

### DON'T

- **Don't query immediately after upload** - Wait for processing to complete
- **Don't use very short queries** - More context = better results
- **Don't forget to authenticate** - Run `orchata login` first
- **Don't mix profile credentials** - Use `--profile` flag consistently

---

## Troubleshooting

### "Command not found: orchata"

**Solution:**
```bash
npm install -g @orchata/cli
orchata --version
```

Verify the CLI is installed and in your PATH.

### "Authentication required"

**Solution:**
```bash
orchata login
# Or set environment variable:
export ORCHATA_API_KEY="oai_..."
```

### "Space not found"

**Solution:**
```bash
orchata spaces list
# Use the exact space ID from the list
```

### "Document still processing"

**Solution:**
Wait 1-3 seconds after upload for processing to complete:
```bash
orchata documents list --space <space_id>
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
  "profiles": {
    "default": {
      "apiKey": "oai_...",
      "apiBase": "https://api.orchata.ai",
      "appBase": "https://app.orchata.ai"
    },
    "production": {
      "apiKey": "oai_...",
      "apiBase": "https://api.orchata.ai"
    }
  },
  "defaultProfile": "default"
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
| **List spaces** | `orchata spaces list` |
| **Create space** | `orchata spaces create --name "Docs"` |
| **Get space** | `orchata spaces get space_123` |
| **List documents** | `orchata documents list --space space_123` |
| **Upload file** | `orchata documents upload ./file.pdf --space space_123` |
| **Batch upload** | `orchata documents batch ./dir/ --space space_123` |
| **Get document** | `orchata documents get doc_123 --space space_123` |
| **Search content** | `orchata query "question" --space space_123` |
| **Discover spaces** | `orchata query smart "question"` |
| **JSON output** | `orchata spaces list --json` |

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
orchata query "summary of document" --space "${SPACE_ID}"
```

### Python: Batch upload with status checking

```python
import subprocess
import json
import time

def run_orchata(command):
    """Run orchata CLI command and return JSON result"""
    cmd = f"orchata {command} --json"
    result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
    return json.loads(result.stdout)

# Create space
space = run_orchata('spaces create --name "My Docs"')
space_id = space['space']['id']

# Batch upload
print(f"Uploading to space: {space_id}")
run_orchata(f'documents batch ./docs/ --space {space_id}')

# Wait for all documents to process
print("Waiting for processing...")
while True:
    docs = run_orchata(f'documents list --space {space_id}')
    pending = [d for d in docs['documents'] if d['status'] != 'COMPLETED']
    
    if not pending:
        print("All documents processed!")
        break
    
    print(f"{len(pending)} documents still processing...")
    time.sleep(3)

# Query
results = run_orchata(f'query "main topics" --space {space_id}')
print(json.dumps(results, indent=2))
```

---

## Differences from MCP Tools

**CLI does NOT support:**
- ❌ Tree-based document navigation (`get_document_tree`, `get_tree_node`)
- ❌ Direct document updates (use upload with same filename to replace)
- ❌ Compact query format (always returns full results)

**CLI excels at:**
- ✅ Batch file operations (`orchata documents batch`)
- ✅ Scripting and automation
- ✅ JSON output for parsing
- ✅ Profile management for multiple environments
- ✅ File system integration

**For tree-based navigation and programmatic access, use the `orchata-mcp` skill instead.**
