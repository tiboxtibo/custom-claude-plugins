---
allowed-tools:
  - Read
  - Glob
  - Grep
argument-hint: "[command|agent] [--examples] [--verbose]"
description: "Sistema di help integrato con esempi e documentazione contestuale"
model: haiku
---

# Toduba Help - Sistema Help Integrato 📖

## Obiettivo
Fornire help contestuale, esempi pratici e documentazione per tutti i componenti del sistema Toduba.

## Argomenti
- `[command|agent]`: Nome specifico comando o agente
- `--examples`: Mostra esempi pratici
- `--verbose`: Documentazione dettagliata
- `--list`: Lista tutti i componenti disponibili
- `--search <term>`: Cerca nella documentazione

Argomenti ricevuti: $ARGUMENTS

## Quick Start Guide

```
╔════════════════════════════════════════════════════════════╗
║                    🚀 TODUBA QUICK START                    ║
╠════════════════════════════════════════════════════════════╣
║                                                              ║
║  1. Initialize project documentation:                       ║
║     /toduba-init                                           ║
║                                                              ║
║  2. Develop a feature:                                     ║
║     "Create a user authentication API"                     ║
║     → Orchestrator handles everything                      ║
║                                                              ║
║  3. Run tests:                                            ║
║     /toduba-test --watch                                  ║
║                                                              ║
║  4. Commit changes:                                       ║
║     /toduba-commit                                        ║
║                                                              ║
║  5. Need help?                                           ║
║     /toduba-help [component]                             ║
║                                                              ║
╚════════════════════════════════════════════════════════════╝
```

## Help System Implementation

### Dynamic Help Generation
```javascript
const generateHelp = (component) => {
  if (!component) {
    return showMainMenu();
  }

  // Check if it's a command
  if (component.startsWith('/') || component.startsWith('toduba-')) {
    return showCommandHelp(component);
  }

  // Check if it's an agent
  if (component.includes('engineer') || component.includes('orchestrator')) {
    return showAgentHelp(component);
  }

  // Search in all documentation
  return searchDocumentation(component);
};
```

## Main Help Menu

```
🎯 TODUBA SYSTEM v2.0 - Help Center
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📚 COMMANDS (5)
────────────────
/toduba-init          Initialize project documentation
/toduba-test          Run test suite with coverage
/toduba-rollback      Rollback to previous state
/toduba-commit        Create structured commits
/toduba-code-review   Perform code review
/toduba-ultra-think   Deep analysis mode
/toduba-update-docs   Update documentation
/toduba-help          This help system

🤖 AGENTS (8)
──────────────
toduba-orchestrator         Brain of the system
toduba-backend-engineer     Backend development
toduba-frontend-engineer    Frontend/UI development
toduba-mobile-engineer      Flutter specialist
toduba-qa-engineer          Test execution
toduba-test-engineer        Test writing
toduba-codebase-analyzer    Code analysis
toduba-documentation-generator  Docs generation

⚡ QUICK TIPS
─────────────
• Start with: /toduba-init
• Orchestrator uses smart mode detection
• Test/QA engineers have different roles
• Docs auto-update for large tasks
• Use /toduba-help <component> for details

Type: /toduba-help <component> --examples for practical examples
```

## Component-Specific Help

### Command Help Template
```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📘 COMMAND: /toduba-[name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 DESCRIPTION
[Brief description of what the command does]

⚙️ SYNTAX
/toduba-[name] [required] [--optional] [--flags]

🎯 ARGUMENTS
• required    Description of required argument
• --optional  Description of optional flag
• --flag      Description of boolean flag

📊 EXAMPLES

Basic usage:
  /toduba-[name]

With options:
  /toduba-[name] --verbose --coverage

Advanced:
  /toduba-[name] pattern --only tests --parallel

💡 TIPS
• [Useful tip 1]
• [Useful tip 2]
• [Common pitfall to avoid]

🔗 RELATED
• /toduba-[related1] - Related command
• toduba-[agent] - Related agent

📚 FULL DOCS
See: commands/toduba-[name].md
```

### Agent Help Template
```markdown
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🤖 AGENT: toduba-[name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🎯 ROLE
[Agent's primary responsibility]

🛠️ CAPABILITIES
• [Capability 1]
• [Capability 2]
• [Capability 3]

📦 TOOLS ACCESS
• Read, Write, Edit
• Bash
• [Other tools]

🔄 WORKFLOW
1. [Step 1 in typical workflow]
2. [Step 2]
3. [Step 3]

📊 WHEN TO USE
✅ Use for:
  • [Scenario 1]
  • [Scenario 2]

❌ Don't use for:
  • [Anti-pattern 1]
  • [Anti-pattern 2]

💡 BEST PRACTICES
• [Best practice 1]
• [Best practice 2]

🔗 WORKS WITH
• toduba-[agent1] - Collaboration pattern
• toduba-[agent2] - Handoff pattern

📚 FULL DOCS
See: agents/toduba-[name].md
```

## Examples System

### Show Examples for Commands
```bash
show_command_examples() {
  case "$1" in
    "toduba-init")
      cat <<EOF
📊 EXAMPLES: /toduba-init

1️⃣ Basic initialization:
   /toduba-init

2️⃣ With verbose output:
   /toduba-init --verbose

3️⃣ Force regeneration:
   /toduba-init --force

4️⃣ After cloning a repo:
   git clone <repo>
   cd <repo>
   /toduba-init

💡 TIP: Always run this first on new projects!
EOF
      ;;

    "toduba-test")
      cat <<EOF
📊 EXAMPLES: /toduba-test

1️⃣ Run all tests:
   /toduba-test

2️⃣ Watch mode for development:
   /toduba-test --watch

3️⃣ With coverage report:
   /toduba-test --coverage

4️⃣ Run specific tests:
   /toduba-test --only "user.*auth"

5️⃣ CI/CD pipeline:
   /toduba-test --coverage --fail-fast

💡 TIP: Use --watch during development!
EOF
      ;;

    "toduba-rollback")
      cat <<EOF
📊 EXAMPLES: /toduba-rollback

1️⃣ Rollback last operation:
   /toduba-rollback

2️⃣ Rollback 3 steps:
   /toduba-rollback --steps 3

3️⃣ Preview without changes:
   /toduba-rollback --dry-run

4️⃣ Rollback to specific commit:
   /toduba-rollback --to abc123def

5️⃣ List available snapshots:
   /toduba-rollback --list

⚠️ CAUTION: Always check --dry-run first!
EOF
      ;;
  esac
}
```

### Show Examples for Agents
```bash
show_agent_examples() {
  case "$1" in
    "toduba-orchestrator")
      cat <<EOF
📊 EXAMPLES: Using toduba-orchestrator

The orchestrator is invoked automatically when you make requests.

1️⃣ Simple request (quick mode):
   "Fix the typo in README"
   → Orchestrator detects simple task, skips ultra-think

2️⃣ Standard request:
   "Add user authentication to the API"
   → Orchestrator does standard analysis, asks for confirmation

3️⃣ Complex request (deep mode):
   "Refactor the entire backend architecture"
   → Full ultra-think analysis with multiple options

💡 The orchestrator automatically detects complexity!
EOF
      ;;

    "toduba-backend-engineer")
      cat <<EOF
📊 EXAMPLES: toduba-backend-engineer tasks

Automatically invoked by orchestrator for:

1️⃣ API Development:
   "Create CRUD endpoints for products"

2️⃣ Database Work:
   "Add indexes to improve query performance"

3️⃣ Integration:
   "Integrate Stripe payment processing"

4️⃣ Performance:
   "Optimize the user search endpoint"

💡 Works in parallel with frontend-engineer!
EOF
      ;;
  esac
}
```

## Search Functionality

```javascript
const searchDocumentation = (term) => {
  console.log(`🔍 Searching for: "${term}"`);
  console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━');

  const results = [];

  // Search in commands
  const commandFiles = glob.sync('commands/toduba-*.md');
  commandFiles.forEach(file => {
    const content = fs.readFileSync(file, 'utf8');
    if (content.toLowerCase().includes(term.toLowerCase())) {
      const lines = content.split('\n');
      const matches = lines.filter(line =>
        line.toLowerCase().includes(term.toLowerCase())
      );
      results.push({
        type: 'command',
        file: path.basename(file, '.md'),
        matches: matches.slice(0, 3)
      });
    }
  });

  // Search in agents
  const agentFiles = glob.sync('agents/toduba-*.md');
  agentFiles.forEach(file => {
    const content = fs.readFileSync(file, 'utf8');
    if (content.toLowerCase().includes(term.toLowerCase())) {
      results.push({
        type: 'agent',
        file: path.basename(file, '.md'),
        context: extractContext(content, term)
      });
    }
  });

  // Display results
  if (results.length === 0) {
    console.log('No results found. Try different terms.');
  } else {
    console.log(`Found ${results.length} matches:\n`);
    results.forEach(displaySearchResult);
  }
};
```

## Interactive Help Mode

```javascript
// When no arguments provided
if (!ARGUMENTS) {
  // Show interactive menu
  console.log('🎯 TODUBA HELP - Interactive Mode');
  console.log('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
  console.log('');
  console.log('What would you like help with?');
  console.log('');
  console.log('1. Commands overview');
  console.log('2. Agents overview');
  console.log('3. Quick start guide');
  console.log('4. Common workflows');
  console.log('5. Troubleshooting');
  console.log('6. Search documentation');
  console.log('');
  console.log('Enter number or type component name:');
}
```

## Common Workflows Section

```markdown
## 🔄 COMMON WORKFLOWS

### 🚀 Starting a New Feature
1. "I want to add user authentication"
2. Orchestrator analyzes (standard mode)
3. Confirms approach with you
4. Delegates to backend/frontend engineers
5. Test engineer writes tests
6. QA engineer runs tests
7. Auto-updates documentation

### 🐛 Fixing a Bug
1. "Fix the login button not working"
2. Orchestrator analyzes (quick/standard)
3. Delegates to appropriate engineer
4. Tests are updated/added
5. QA validates fix

### 📊 Code Analysis
1. /toduba-code-review
2. Analyzer examines code
3. Provides recommendations
4. Can trigger refactoring

### 🔄 Deployment Preparation
1. /toduba-test --coverage
2. /toduba-code-review
3. /toduba-commit
4. Ready for deployment!
```

## Troubleshooting Section

```markdown
## 🔧 TROUBLESHOOTING

### ❌ Common Issues

#### "Orchestrator not responding"
• Check if Claude Desktop is running
• Restart Claude Desktop
• Check .claude-plugin/marketplace.json

#### "Test command not finding tests"
• Ensure test files follow naming convention
• Check test runner is installed
• Run: npm install (or equivalent)

#### "Rollback failed"
• Check .toduba/snapshots/ exists
• Ensure sufficient disk space
• Try: /toduba-rollback --list

#### "Documentation not updating"
• Run: /toduba-update-docs --force
• Check /docs directory permissions
• Verify git status

### 💡 Pro Tips
• Use --verbose for debugging
• Check logs in .toduba/logs/
• Join Discord for community help
```

## Output Format

```
╔═══════════════════════════════════════════╗
║          TODUBA HELP SYSTEM               ║
╠═══════════════════════════════════════════╣
║                                           ║
║  Topic: [Component Name]                 ║
║  Type: [Command/Agent/Workflow]          ║
║                                           ║
║  [Help content here]                     ║
║                                           ║
║  Need more? Try:                         ║
║  • /toduba-help [topic] --examples       ║
║  • /toduba-help --search [term]          ║
║                                           ║
╚═══════════════════════════════════════════╝
```