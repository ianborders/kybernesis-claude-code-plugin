---
name: kybernesis-agents
description: Chat with your personalized Kybernesis AI agents. Use when user wants to list agents, talk to an agent by name, or interact with their Kybernesis workspace.
allowed-tools: Bash(curl -s "https://api.kybernesis.ai:*), Bash(curl -s -X POST "https://api.kybernesis.ai:*)
---

# Kybernesis Agents

Connect to the user's personalized AI agents powered by Kybernesis. Each agent has its own personality, expertise, and access to workspace memories.

## When to Use

Use this skill when:
- The user wants to talk to one of their Kybernesis agents
- The user mentions an agent by name (e.g., "ask Samantha about...", "talk to my assistant")
- The user wants to list their available agents
- The user asks about their Kybernesis workspace or memories

## Setup Requirements

The user must have `KYBERNESIS_API_KEY` set in their environment:
```bash
export KYBERNESIS_API_KEY="kb_your_api_key_here"
```

Get an API key from: https://kybernesis.ai/settings/api-keys

## How to List Agents

First, discover what agents the user has available:

```bash
curl -s "https://api.kybernesis.ai/v1/agents" \
  -H "Authorization: Bearer $KYBERNESIS_API_KEY"
```

Response format:
```json
{
  "agents": [
    {
      "id": "abc123",
      "name": "Samantha",
      "description": "A thoughtful AI companion",
      "status": "active",
      "modelId": "claude-sonnet-4-20250514",
      "tags": ["personal", "assistant"]
    }
  ],
  "count": 1
}
```

## How to Chat with an Agent

Send a message to a specific agent using their ID:

```bash
curl -s -X POST "https://api.kybernesis.ai/v1/agents/{AGENT_ID}/chat" \
  -H "Authorization: Bearer $KYBERNESIS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello, how are you?"}'
```

Response format:
```json
{
  "response": "The agent's response text here",
  "conversationId": "conv_xxx",
  "memoriesUsed": 3,
  "memoryBlocksUpdated": [],
  "tokenCount": {"input": 150, "output": 200}
}
```

## Maintaining Conversations

To continue a conversation, pass the `conversationId` from the previous response:

```bash
curl -s -X POST "https://api.kybernesis.ai/v1/agents/{AGENT_ID}/chat" \
  -H "Authorization: Bearer $KYBERNESIS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"message": "Tell me more", "conversationId": "conv_xxx"}'
```

## Workflow

1. **If user asks to talk to an agent by name**:
   - FIRST: Call the list endpoint to get all agents with their IDs
   - SECOND: Find the agent with the matching name (case-insensitive)
   - THIRD: Use the agent's `id` field (NOT the name) to call the chat endpoint
   - Example: If user says "talk to Samantha" and Samantha has id "p1788...", use that ID in the chat URL
2. **If user says "list my agents"**: Call the list endpoint and show the results in a table
3. **If continuing a conversation**: Use the stored conversationId from previous interactions
4. **Always show the agent's response** to the user after receiving it

**CRITICAL**: The chat endpoint requires the agent's `id` field, NOT the agent's name. Always get the ID from the list response first.

## Important Notes

- Each agent has access to the user's workspace memories and can search them contextually
- Agents remember their conversations (use conversationId to maintain context)
- The `memoriesUsed` field shows how many workspace memories influenced the response
- Agents can update their own memory blocks during conversation
