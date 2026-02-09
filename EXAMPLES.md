# SimpleMem Example Usage

This file demonstrates how to use the SimpleMem skill functions.

## Prerequisites

1. SimpleMem server running: `python run.py --port 8001` in MCP folder
2. mcporter configured with the `simplemem` server
3. Ollama models available: `qwen3:4b` and `qwen3-embedding:4B`

## Example 1: Store a Dialogue

```bash
# Store a simple memory
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: 'Setting up SimpleMem with Ollama for local memory storage')"

# Store a more complex dialogue
mcporter call "simplemem.memory_add(speaker: 'Bob', content: 'I'll meet Alice at Starbucks tomorrow at 2pm to discuss the project')"
```

## Example 2: Query Memories

```bash
# Ask a simple question
mcporter call "simplemem.memory_query(question: 'What is Daniel setting up?')"

# Ask a complex question
mcporter call "simplemem.memory_query(question: 'What meetings did I have last week?', enable_reflection: true)"

# Ask about a specific topic
mcporter call "simplemem.memory_query(question: 'What does Alice think about the project?')"
```

## Example 3: Retrieve Raw Entries

```bash
# Get raw memory entries about a topic
mcporter call "simplemem.memory_retrieve(query: 'project timeline', top_k: 5)"

# Get all entries (use with caution)
mcporter call "simplemem.memory_retrieve(query: '', top_k: 10)"
```

## Example 4: Batch Operations

```bash
# Store multiple dialogues at once
mcporter call "simplemem.memory_add_batch(dialogues: [
  'Met with Bob at Starbucks yesterday at 2pm to discuss project timeline',
  'Alice will review the documentation tomorrow morning',
  'Scheduled a call with the client for next Tuesday at 3pm'
])"
```

## Example 5: Check Status

```bash
# Get memory statistics
mcporter call "simplemem.memory_stats()"

# Clear all memories (use with caution)
mcporter call "simplemem.memory_clear()"
```

## Example 6: Integration with Other Agents

From another skill or agent:

```javascript
import { memory_add, memory_query } from '@simplemem';

// Store a memory when completing a task
memory_add(speaker: "Agent", content: "Completed task XYZ and updated the documentation");

// Query memories when asked about recent work
const result = memory_query(question: "What did Agent do today?");
console.log(result.answer);
```

## Example 7: Error Handling

```bash
# Check if server is running
curl http://127.0.0.1:8001/mcp

# Test connection
mcporter list simplemem

# If tools not found, restart mcporter
mcporter restart
```

## Example 8: Performance Testing

```bash
# Add 100 memories quickly
for i in {1..100}; do
  mcporter call "simplemem.memory_add(speaker: 'Test', content: 'This is test memory number $i')"
done

# Query the memories
mcporter call "simplemem.memory_query(question: 'What test memories did I add?')"
```

## Example 9: Real-world Usage

```bash
# Store meeting notes
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: 'Had a productive meeting with the development team. We discussed the new API endpoints and decided to implement them using REST conventions. The timeline looks good for the next sprint.')"

# Store ideas
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: 'Idea: Create a local memory system using Ollama for semantic search. This would allow agents to have long-term memory without cloud dependencies.')"

# Store decisions
mcporter call "simplemem.memory_add(speaker: 'Daniel', content: 'Decided to use qwen3:4b for chat and qwen3-embedding:4B for embeddings. This fits well within the 8GB VRAM limit.')"
```

## Example 10: Advanced Queries

```bash
# Multi-hop reasoning
mcporter call "simplemem.memory_query(question: 'What projects did Alice work on last month that involved Bob?', enable_reflection: true)"

# Temporal queries
mcporter call "simplemem.memory_query(question: 'What did I work on last week?')"

# Entity-based queries
mcporter call "simplemem.memory_query(question: 'What does the client think about the new feature?')"
```

## Tips

1. **Use absolute timestamps** in content for better temporal anchoring
2. **Include speaker names** for better coreference resolution
3. **Be specific** in queries for better results
4. **Use `enable_reflection`** for complex multi-hop queries
5. **Check `memory_stats()`** to see how many memories are stored