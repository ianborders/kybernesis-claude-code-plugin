---
allowed-tools: Bash(npx kybernesis-plugin login:*)
description: Authenticate with Kybernesis via browser login
---

# Kybernesis Login

Run the Kybernesis authentication flow. This will:
1. Open your browser to kybernesis.ai
2. You'll sign in with your existing account
3. Click "Authorize" to grant Claude Code access
4. Your API key will be saved securely to ~/.kybernesis/api-key

Execute this command:

```bash
npx kybernesis-plugin login
```

After successful authentication, inform the user they can now use the kybernesis-agents skill to chat with their agents.
