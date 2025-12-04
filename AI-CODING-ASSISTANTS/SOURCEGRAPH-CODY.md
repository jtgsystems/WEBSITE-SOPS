# Sourcegraph Cody - AI Coding Assistant SOP

**Version:** 1.0
**Last Updated:** December 2024
**Source:** [docs.sourcegraph.com/cody](https://docs.sourcegraph.com/cody)

---

## Overview

Sourcegraph Cody is an AI coding assistant that uses deep code understanding to help developers write, understand, and fix code faster. Unlike other AI assistants, Cody leverages Sourcegraph's code intelligence platform to provide context-aware assistance across your entire codebase.

## Key Features

### 1. Context-Aware Code Intelligence
- **Codebase-wide understanding**: Indexes and understands your entire codebase
- **Cross-repository search**: Find code patterns across multiple repositories
- **Semantic code search**: Natural language queries for code discovery
- **Symbol navigation**: Jump to definitions, references, and implementations

### 2. AI-Powered Capabilities
- **Code completion**: Context-aware inline suggestions
- **Code generation**: Generate functions, classes, and boilerplate
- **Code explanation**: Understand complex code blocks
- **Bug detection**: Identify potential issues and vulnerabilities
- **Refactoring assistance**: Suggestions for code improvements
- **Documentation generation**: Auto-generate docstrings and comments

### 3. Multi-LLM Support
- Claude (Anthropic)
- GPT-4 (OpenAI)
- Gemini (Google)
- Mixtral and other open models
- Bring Your Own Key (BYOK) support

---

## Installation & Setup

### VS Code Extension

```bash
# Install from VS Code Marketplace
1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Search "Sourcegraph Cody"
4. Click Install
```

### JetBrains IDEs

```bash
# Install from JetBrains Marketplace
1. Open Settings/Preferences
2. Go to Plugins
3. Search "Sourcegraph Cody"
4. Click Install and Restart
```

### Neovim

```lua
-- Add to your plugin configuration
{
  "sourcegraph/sg.nvim",
  dependencies = { "nvim-lua/plenary.nvim" },
  config = function()
    require("sg").setup()
  end
}
```

### Authentication

```bash
# Sign in with Sourcegraph account
1. Click Cody icon in sidebar
2. Select "Sign in with Sourcegraph.com"
3. Authorize in browser
4. Return to IDE

# Enterprise setup
1. Enter your Sourcegraph instance URL
2. Generate access token from Settings > Access Tokens
3. Enter token in Cody settings
```

---

## Configuration

### VS Code Settings

```json
{
  // Cody settings
  "cody.serverEndpoint": "https://sourcegraph.com",
  "cody.autocomplete.enabled": true,
  "cody.autocomplete.languages": {
    "typescript": true,
    "javascript": true,
    "python": true,
    "go": true
  },
  "cody.experimental.symf": true,
  "cody.chat.preInstruction": "You are helping with a Next.js TypeScript project",

  // Context settings
  "cody.codebase": "github.com/your-org/your-repo",
  "cody.useContext": "embeddings"
}
```

### Context Configuration

```json
{
  // .sourcegraph/cody.json
  "contextConfig": {
    "include": [
      "src/**/*",
      "lib/**/*",
      "docs/**/*"
    ],
    "exclude": [
      "node_modules/**",
      "dist/**",
      "*.test.ts"
    ]
  },
  "chat": {
    "preInstruction": "This is a production web application using React and TypeScript."
  }
}
```

---

## Core Workflows

### 1. Code Completion

```typescript
// Cody provides intelligent completions as you type

// Type a function signature, Cody completes the implementation
function calculateDiscount(price: number, percentage: number)
// Cody suggests:
// {
//   return price * (1 - percentage / 100);
// }

// Type a comment, Cody generates the code
// Create a debounce function with configurable delay
// Cody generates complete implementation
```

### 2. Chat Interface

```
Commands in chat:
/explain     - Explain selected code
/edit        - Edit code with instructions
/test        - Generate unit tests
/doc         - Generate documentation
/smell       - Identify code smells
/fix         - Fix bugs or issues
```

### 3. Code Search

```
Natural language queries:
- "Where is user authentication handled?"
- "Find all API endpoints that require admin access"
- "Show me examples of error handling patterns"
- "Which files import the UserService class?"
```

### 4. Inline Editing

```typescript
// Select code block, then use Edit command
// Original:
function getUser(id) {
  return db.query(`SELECT * FROM users WHERE id = ${id}`);
}

// Instruction: "Make this function safe from SQL injection and add TypeScript types"

// Cody generates:
async function getUser(id: string): Promise<User | null> {
  const result = await db.query(
    'SELECT * FROM users WHERE id = $1',
    [id]
  );
  return result.rows[0] ?? null;
}
```

---

## Enterprise Features

### Code Graph Integration

```yaml
# sourcegraph.yaml - Repository indexing configuration
index:
  - language: typescript
    patterns:
      - "**/*.ts"
      - "**/*.tsx"
    exclude:
      - "node_modules/**"
      - "**/*.test.ts"

  - language: go
    patterns:
      - "**/*.go"
    exclude:
      - "vendor/**"
```

### Custom Context Sources

```json
{
  "context": {
    "repositories": [
      "github.com/company/main-app",
      "github.com/company/shared-libs",
      "github.com/company/internal-docs"
    ],
    "embeddings": {
      "enabled": true,
      "provider": "sourcegraph"
    }
  }
}
```

### Access Controls

```yaml
# Enterprise access configuration
permissions:
  - team: frontend
    repositories:
      - "github.com/company/web-app"
      - "github.com/company/ui-components"

  - team: backend
    repositories:
      - "github.com/company/api-server"
      - "github.com/company/services/*"
```

---

## Best Practices

### 1. Context Optimization

```markdown
DO:
- Keep relevant files open in your editor
- Use specific repository context for better answers
- Include documentation in indexed content
- Configure language-specific completions

DON'T:
- Include generated or minified files
- Index large binary files or assets
- Ignore .gitignore patterns
```

### 2. Effective Prompting

```markdown
Good prompts:
- "Refactor this function to use async/await instead of callbacks"
- "Add comprehensive error handling to this API endpoint"
- "Generate unit tests that cover edge cases for the validateEmail function"

Avoid:
- "Fix this" (too vague)
- "Make it better" (no specific direction)
- "Write code" (no context)
```

### 3. Security Considerations

```markdown
Security best practices:
- Review all AI-generated code before committing
- Don't share sensitive data in prompts
- Use enterprise deployment for proprietary codebases
- Enable audit logging for compliance
- Restrict context access to appropriate repositories
```

---

## Integration with Development Workflow

### Git Integration

```bash
# Use Cody for commit messages
git diff --staged | cody generate-commit-message

# Code review assistance
cody review --pr 123 --check security,performance
```

### CI/CD Integration

```yaml
# .github/workflows/cody-review.yml
name: Cody Code Review

on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: sourcegraph/cody-action@v1
        with:
          token: ${{ secrets.SOURCEGRAPH_TOKEN }}
          checks:
            - security
            - performance
            - best-practices
```

### Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: cody-review
        name: Cody Quick Review
        entry: cody review --quick
        language: system
        types: [python, typescript, javascript]
```

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Slow completions | Check network connection; reduce context size |
| No context awareness | Verify repository is indexed; check embeddings status |
| Authentication errors | Regenerate access token; verify endpoint URL |
| Rate limiting | Upgrade plan or use enterprise deployment |

### Debug Mode

```json
{
  "cody.debug.enable": true,
  "cody.debug.verbose": true
}
```

### Logs Location

```bash
# VS Code
~/.config/Code/logs/*/exthost/Sourcegraph.cody-ai/

# JetBrains
~/.cache/JetBrains/<IDE>/log/sourcegraph/
```

---

## Comparison with Other AI Assistants

| Feature | Cody | Copilot | Cursor |
|---------|------|---------|--------|
| Codebase context | Full repo | Current file | Project files |
| Multi-repo search | Yes | No | Limited |
| Self-hosted option | Yes | No | No |
| LLM choice | Multiple | GPT-4 | Multiple |
| Code search | Advanced | Basic | Basic |
| Enterprise features | Extensive | Limited | Limited |

---

## Resources

### Official Documentation
- [Cody Documentation](https://docs.sourcegraph.com/cody)
- [Sourcegraph Blog](https://about.sourcegraph.com/blog)
- [API Reference](https://docs.sourcegraph.com/api)

### Community
- [Discord](https://discord.gg/sourcegraph)
- [GitHub Discussions](https://github.com/sourcegraph/sourcegraph/discussions)
- [Twitter/X](https://twitter.com/sourcegraph)

---

**Document Version:** 1.0
**Maintained by:** Development Team
**Review Cycle:** Quarterly
