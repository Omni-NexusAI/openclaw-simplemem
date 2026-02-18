---
name: simplemem
description: Local memory storage with Ollama semantic search for persistent agent memory
metadata:
  {
    "openclaw":
      {
        "emoji": "🧠",
        "requires": { "bins": ["mcporter"] },
      },
  }
---

# SimpleMem Skill

A skill for interacting with SimpleMem, a local memory storage system that uses Ollama for semantic search and dialogue compression.

## Overview

SimpleMem is a self-hosted MCP server that provides long-term memory storage for OpenClaw agents. It uses:
- **Ollama** for local LLM inference (qwen3:4b)
- **qwen3-embedding:4B** for semantic embeddings
- **Local storage** (no cloud dependencies)

## Setup

### Prerequisites
- SimpleMem cloned to `C:\Users\yepyy\.openclaw\SimpleMem`
- Ollama running with `qwen3:4b` and `qwen3-embedding:4B` models
- MCP server started: `python run.py --port 8001` in `C:\Users\yepyy\.openclaw\SimpleMem\MCP`

### Configuration
1. Register user and get token via:
   ```bash
   curl -X POST http://127.0.0.1:8001/api/auth/register -H "Content-Type: application/json" -d '{"openrouter_api_key": "local-ollama-user"}'
   ```
2. Add to mcporter config:
   ```bash
   mcporter config add simplemem --url http://127.0.0.1:8001/mcp --header Authorization="Bearer YOUR_TOKEN_HERE"
   ```

## Functions

### memory_add(speaker, content, timestamp?)
Store a dialogue in SimpleMem memory.

**Parameters:**
- `speaker`: Name of the speaker (used for coreference resolution)
- `content`: Content of the dialogue (pronouns will be resolved)
- `timestamp?`: ISO 8601 timestamp (defaults to now)

**Example:**
```
memory_add(speaker: "Daniel", content: "Setting up SimpleMem with Ollama for local memory storage")
```

### memory_add_batch(dialogues)
Store multiple dialogues at once.

**Parameters:**
- `dialogues`: Array of dialogue strings

**Example:**
```
memory_add_batch(dialogues: ["First dialogue", "Second dialogue", "Third dialogue"])
```

### memory_query(question, enable_reflection?)
Query SimpleMem and get an AI-generated answer.

**Parameters:**
- `question`: Natural language question
- `enable_reflection?`: Enable iterative refinement for complex queries (default: true)

**Example:**
```
memory_query(question: "What is Daniel setting up?")
```

### memory_retrieve(query, top_k?)
Retrieve raw memory entries without generating an answer.

**Parameters:**
- `query`: Search query (semantic + keyword matching)
- `top_k?`: Maximum number of entries to return (default: 10)

**Example:**
```
memory_retrieve(query: "SimpleMem setup", top_k: 5)
```

### memory_clear()
Clear ALL memories for this user. This action CANNOT be undone.

**Example:**
```
memory_clear()
```

### memory_stats()
Get statistics about the memory store.

**Example:**
```
memory_stats()
```

## Usage

### From Other Skills
```javascript
import { memory_add, memory_query } from '@simplemem';

// Store a memory
memory_add(speaker: "Agent", content: "Completed task XYZ");

// Query memories
const result = memory_query(question: "What did Agent do today?");
```

### From Main Session
```bash
# Add memory
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: '...' )"

# Query memory
mcporter call "simplemem.memory_query(question: '...')"
```

## Technical Details

### Models Used
- **Chat Model**: `qwen3:4b` (2.5GB) - for dialogue compression and query generation
- **Embedding Model**: `qwen3-embedding:4B` (2.5GB) - for semantic search
- **Total VRAM**: ~5GB (fits in 8GB GPU with headroom)

### Data Storage
- **Vector Database**: LanceDB for semantic embeddings
- **Metadata**: SQLite database for structured data
- **Location**: `C:\Users\yepyy\.openclaw\SimpleMem\data`

### API Endpoints
- **Server**: `http://127.0.0.1:8001`
- **MCP**: `/mcp`
- **Auth**: `/api/auth/register`

## Troubleshooting

### Server Not Running
```bash
cd C:\Users\yepyy\.openclaw\SimpleMem\MCP
python run.py --port 8001
```

### Models Not Found
```bash
ollama list
# Should show: qwen3:4b and qwen3-embedding:4B
```

### Connection Issues
- Check server is running on port 8001
- Verify token is correct in mcporter config
- Ensure firewall allows localhost connections

## Examples

### Store Meeting Notes
```
memory_add(speaker: "Daniel", content: "Met with Bob at Starbucks yesterday at 2pm to discuss project timeline")
```

### Query Recent Events
```
memory_query(question: "What meetings did I have last week?")
```

### Search by Topic
```
memory_retrieve(query: "project timeline", top_k: 3)
```

## Development

### Adding New Functions
1. Update the MCP server code in `C:\Users\yepyy\.openclaw\SimpleMem\MCP`
2. Restart the server
3. Update this skill file with new function documentation

### Performance Tips
- Use `memory_add_batch` for importing conversation history
- Disable `enable_reflection` for simple factual lookups
- Use `memory_retrieve` when you need raw data instead of generated answers

## License

SimpleMem is open source. This skill is provided as-is for OpenClaw integration.