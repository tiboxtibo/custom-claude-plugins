---
name: context-gather
description: Foundation skill for gathering project context - retrieves requirements, specifications, and relevant documentation to prepare for development work
---

# Context Gathering Skill

## Overview

Reusable context gathering workflow for development tasks. This skill helps you systematically collect all necessary information before starting implementation.

**Announce at start:** "I'm using the context-gather skill to gather full context for this work."

## When to Use

Use this skill when:
- Starting any development task
- You need to understand requirements and acceptance criteria
- You're preparing for implementation or code review
- You want to ensure you have all necessary context

## Context Gathering Process

### Step 1: Understand the Request

**Gather from user:**
- What needs to be built or fixed?
- What are the acceptance criteria?
- Are there any design mockups or specifications?
- What is the priority and timeline?
- Are there any constraints or dependencies?

**Ask clarifying questions if needed:**
- "Can you describe the expected behavior?"
- "Are there any existing similar features I should reference?"
- "What are the most important aspects of this work?"
- "Are there any technical constraints I should know about?"

### Step 2: Review Existing Documentation

**Look for relevant information:**
- Project README and documentation
- Existing code that might need modification
- Design specifications or mockups
- API documentation
- Architecture diagrams
- Similar features or components

**Use grep/glob to find relevant code:**
- Search for related functionality
- Find existing patterns to follow
- Identify integration points
- Locate test files

### Step 3: Understand the Codebase Context

**Explore the project structure:**
- Identify where new code should go
- Find existing conventions and patterns
- Locate configuration files
- Understand the build and test setup

**Review related code:**
- Read similar implementations
- Understand existing APIs and interfaces
- Review database schemas if applicable
- Check state management patterns

### Step 4: Identify Dependencies

**Map out what you'll need:**
- External APIs or services
- Database changes
- Frontend-backend integration points
- Third-party libraries
- Environment variables or configuration

**Clarify with user or Atlas:**
- "I'll need API endpoint [X] - should I create it or does it exist?"
- "This feature will require database changes - should I proceed?"
- "I need clarification on how [component] should integrate with [system]"

### Step 5: Define Scope and Approach

**Create a clear plan:**
- List what will be implemented
- Identify what's out of scope
- Note any assumptions
- Highlight potential challenges

**Confirm with user if needed:**
- "Based on my understanding, I'll implement [X, Y, Z]. Does this match your expectations?"
- "I'm assuming [assumption] - is this correct?"

## Output Structure

Present gathered context in this format:

```markdown
# Context for: [Task Description]

## Requirements
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

## Acceptance Criteria
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

## Technical Context
- **Tech Stack**: [Technologies being used]
- **Architecture**: [Relevant architectural patterns]
- **Integration Points**: [APIs, services, components to integrate with]

## Existing Code to Reference
- `path/to/similar/feature.ts` - [Description]
- `path/to/api/endpoint.ts` - [Description]

## Dependencies
- [Dependency 1]
- [Dependency 2]

## Implementation Approach
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Open Questions
- [Question 1]
- [Question 2]

## Next Steps
- [Next action 1]
- [Next action 2]
```

## Best Practices

### DO:
- ✓ Ask clarifying questions when requirements are unclear
- ✓ Review existing code for patterns to follow
- ✓ Identify all integration points
- ✓ Document assumptions
- ✓ Confirm understanding with user before proceeding
- ✓ Look for similar implementations to reference

### DON'T:
- ✗ Make assumptions without documenting them
- ✗ Skip understanding existing patterns
- ✗ Start implementation without clear requirements
- ✗ Ignore potential dependencies
- ✗ Proceed if critical information is missing

## Next Steps After Context Gathering

After gathering context, proceed with:
- **For implementation**: Use `tdd-workflow` skill if writing tests
- **For code review**: Use `code-review` skill
- **For debugging**: Start investigating with full context
- **For architecture**: Design the solution based on gathered context

## Error Handling

**If requirements are unclear:**
- Ask specific questions to the user
- Document what you do understand
- Note what needs clarification
- Propose possible interpretations

**If code is hard to understand:**
- Start with high-level understanding
- Focus on integration points first
- Ask for guidance if stuck
- Document what you learn

**If context is incomplete:**
- Work with what you have
- Document missing pieces
- Ask user to provide additional information
- Proceed cautiously with clearly stated assumptions

## Success Criteria

Context gathering is successful when you can answer:
- ✓ What exactly needs to be built?
- ✓ How will I know when it's done?
- ✓ Where in the codebase does this go?
- ✓ What existing code or patterns should I follow?
- ✓ What are the integration points?
- ✓ What are the potential challenges?
- ✓ Do I have everything I need to start?

## Example: Gathering Context for a New Feature

```
User: "Add a user profile page"

Context Gathering:
1. Requirements:
   - Display user information (name, email, avatar)
   - Allow users to edit their profile
   - Show user activity history

2. Existing Code Review:
   - Found similar page at `/pages/settings/index.tsx`
   - User data is fetched from `/api/users/[id]`
   - Form validation uses Zod schemas in `/lib/validations/`

3. Dependencies:
   - Need to create `/pages/profile/[id].tsx`
   - Need API route `/api/users/[id]/profile` (or extend existing)
   - Need to integrate with existing auth system

4. Implementation Approach:
   - Create new page using existing settings page as template
   - Reuse existing user API with profile-specific fields
   - Follow existing form patterns for profile editing
   - Add tests similar to settings page tests

5. Clarifying Questions:
   - Should all users be able to view any profile, or only their own?
   - What fields should be editable?
   - Should we show activity history on initial release?
```

This systematic approach ensures you have all the context needed before starting work, reducing rework and misunderstandings.
