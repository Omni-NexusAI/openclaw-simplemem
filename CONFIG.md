# SimpleMem Skill Configuration

This file contains the mcporter configuration for connecting to the SimpleMem MCP server.

## Configuration

The SimpleMem MCP server is configured with:
- **Server**: `http://127.0.0.1:8001/mcp`
- **Authentication**: Bearer token (obtained via registration)
- **Name**: `simplemem`

## Setup Steps

1. **Register a user** (get JWT token):
   ```bash
   curl -X POST http://127.0.0.1:8001/api/auth/register -H "Content-Type: application/json" -d '{"openrouter_api_key": "local-ollama-user"}'
   ```

2. **Add to mcporter config** (update the token):
   ```bash
   mcporter config add simplemem --url http://127.0.0.1:8001/mcp --header Authorization="Bearer YOUR_TOKEN_HERE"
   ```

3. **Test the connection**:
   ```bash
   mcporter list simplemem
   mcporter call simplemem.memory_stats
   ```

## Token Storage

The JWT token should be stored securely. For development, it can be kept in this configuration file, but for production:
- Use environment variables
- Use a secure credential store
- Avoid committing tokens to version control

## Server Status

The SimpleMem server should be running on port 8001:
```bash
cd C:\Users\yepyy\.openclaw\SimpleMem\MCP
python run.py --port 8001
```

## Available Tools

Once configured, the following tools are available via mcporter:
- `memory_add(speaker, content, timestamp?)`
- `memory_add_batch(dialogues)`
- `memory_query(question, enable_reflection?)`
- `memory_retrieve(query, top_k?)`
- `memory_clear()`
- `memory_stats()`

## Usage Examples

### From Shell
```bash
# Add a memory
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: 'Setting up SimpleMem with Ollama')"

# Query memories
mcporter call "simplemem.memory_query(question: 'What am I working on?')"
```

### From Other Skills
```javascript
import { memory_add, memory_query } from '@simplemem';

// Store a memory
memory_add(speaker: "Agent", content: "Completed task XYZ");

// Query memories
const result = memory_query(question: "What did Agent do today?");
```

## Troubleshooting

### Connection Refused
- Ensure SimpleMem server is running: `python run.py --port 8001`
- Check the URL in mcporter config
- Verify the token is correct

### Token Expired
- Re-register to get a new token
- Update the mcporter config with the new token

### Tools Not Found
- Verify the server is running
- Check the MCP endpoint is correct
- Restart mcporter if needed

## Performance

The SimpleMem server uses local Ollama models:
- **Chat Model**: `qwen3:4b` (2.5GB) - for dialogue compression and query generation
- **Embedding Model**: `qwen3-embedding:4B` (2.5GB) - for semantic search
- **Total VRAM**: ~5GB (fits in 8GB GPU with headroom)

## Security

- The server runs on localhost only (127.0.0.1)
- JWT tokens are used for authentication
- No external API dependencies
- All data stored locally in `C:\Users\yepyy\.openclaw\SimpleMem\data`