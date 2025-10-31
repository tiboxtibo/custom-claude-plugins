---
name: init
description: Initialize comprehensive codebase documentation for faster agent operations
---

# Initialize Codebase Documentation

Generate comprehensive documentation of the current codebase that will serve as a knowledge base for all future agent operations. This dramatically improves performance by allowing agents to read documentation instead of analyzing the entire codebase each time.

## Purpose

When working on a new codebase, run `/init` as the FIRST command to:
1. Analyze the entire codebase structure
2. Generate comprehensive documentation in `.claude-docs/`
3. Create a knowledge base that agents can quickly reference
4. Improve response times for all subsequent operations

## Process Overview

### Phase 1: Pre-Analysis (1 minute)

1. **Check Existing Documentation**
   - Look for `.claude-docs/` directory
   - If exists, ask user if they want to regenerate or update
   - Check for README, CONTRIBUTING, or other existing docs

2. **Identify Project Type**
   - Check package.json, requirements.txt, go.mod, pom.xml, etc.
   - Detect framework (React, Next.js, Django, Spring, etc.)
   - Determine if monorepo or single project

3. **Estimate Complexity**
   - Count files and directories
   - Identify main code directories
   - Estimate time needed (inform user: ~5-15 minutes)

### Phase 2: Parallel Analysis (5-10 minutes)

Launch multiple codebase-analyzer agents IN PARALLEL to speed up analysis:

```
Group 1: Structure & Dependencies
- Agent 1: Project structure and architecture
- Agent 2: Dependencies and configuration

Group 2: Code Analysis
- Agent 3: API endpoints and routes
- Agent 4: Database schema and models

Group 3: Frontend (if applicable)
- Agent 5: Components and UI structure
- Agent 6: State management and data flow

Group 4: Patterns & Quality
- Agent 7: Code patterns and conventions
- Agent 8: Testing and quality setup
```

**Launch Strategy**:
- Use Task tool with multiple parallel calls in a single message
- Each agent focuses on specific aspect
- Agents write to different documentation files to avoid conflicts

### Phase 3: Documentation Generation (2-3 minutes)

Each agent creates specific documentation files:

#### Core Documentation Structure

```
.claude-docs/
├── PROJECT_OVERVIEW.md     # Project type, tech stack, features
├── ARCHITECTURE.md          # High-level architecture, patterns
├── API_ENDPOINTS.md         # All API routes and contracts
├── DATABASE_SCHEMA.md       # Tables, relationships, queries
├── COMPONENTS.md            # UI components and hierarchy
├── DEPENDENCIES.md          # Package analysis and purpose
├── CONFIGURATION.md         # Env vars, build, deployment
├── PATTERNS.md             # Code conventions and patterns
├── TESTING.md              # Test structure and commands
├── QUICK_START.md          # How to run, build, test
└── INDEX.md                # Table of contents with summaries
```

### Phase 4: Synthesis (1 minute)

1. **Create INDEX.md**
   - Summary of each documentation file
   - Quick navigation links
   - Key insights and recommendations

2. **Generate CLAUDE.md** (if not exists)
   - Specific guidance for Claude agents
   - Common tasks and commands
   - Architecture highlights

3. **Create TODO.md**
   - Areas needing attention
   - Technical debt identified
   - Missing documentation

## Usage Instructions

### For New Codebase

```bash
# First time on a project
/init

# Claude will:
# 1. Analyze your entire codebase
# 2. Generate documentation in .claude-docs/
# 3. Create CLAUDE.md with specific guidance
# 4. Report completion with summary
```

### For Updated Codebase

```bash
# After significant changes
/init --update

# Updates only changed areas
# Preserves custom documentation
```

### For Specific Areas

```bash
# Document only specific aspects
/init --focus=api,database
/init --focus=frontend
```

## Benefits for Agents

After `/init` completion, agents can:

1. **Read `.claude-docs/ARCHITECTURE.md`** instead of analyzing file structure
2. **Check `.claude-docs/API_ENDPOINTS.md`** instead of searching for routes
3. **Reference `.claude-docs/PATTERNS.md`** instead of inferring conventions
4. **Use `.claude-docs/QUICK_START.md`** for build/test commands

This results in:
- 5-10x faster response times
- More accurate understanding
- Consistent knowledge across agents
- Better architectural decisions

## Implementation Details

### Parallel Agent Invocation

```python
# Launch all agents in a single message for parallel execution
Task(subagent_type="codebase-analyzer", prompt="Analyze project structure...")
Task(subagent_type="codebase-analyzer", prompt="Analyze API endpoints...")
Task(subagent_type="codebase-analyzer", prompt="Analyze database schema...")
# ... more agents
```

### Documentation Format

Each file follows this structure:
```markdown
# [Topic]
*Generated: [timestamp]*
*Project: [name]*

## Overview
[1-2 paragraph summary]

## Details
[Comprehensive information]

## Quick Reference
[Bullet points of key items]

## Related Files
[List of relevant source files]
```

## Progress Reporting

Show real-time progress to user:
```
🔍 Initializing codebase documentation...
📊 Project type detected: Next.js + MongoDB
🚀 Launching parallel analysis agents...
   ✓ Structure analysis complete
   ✓ API documentation complete
   ⚡ Database analysis in progress...
   ⚡ Component mapping in progress...
📝 Generating documentation files...
✅ Documentation complete! Find it in .claude-docs/
```

## Error Handling

- If codebase is too large (>10k files), focus on core directories
- If analysis fails, continue with partial documentation
- Always create at least PROJECT_OVERVIEW.md and ARCHITECTURE.md

## Notes

- Documentation is project-specific, not plugin documentation
- Designed to run once at project start, then occasionally for updates
- Creates persistent knowledge base for all future operations
- Significantly improves agent performance and accuracy