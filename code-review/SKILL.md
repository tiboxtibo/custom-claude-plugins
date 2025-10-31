---
name: code-review
description: Systematic code review with plan→journal→code traceability, requirements verification, and cross-repository integration checks
---

# Code Review Skill

## Overview

Systematic code review with three-layer verification: plan→journal→code traceability, requirements compliance, and cross-repository integration validation.

**Announce at start:** "I'm using the code-review skill to review this implementation."

**Dependencies:** Requires `context-gather` skill as foundation

## When to Use

Use this skill when:
- Reviewing implementation work by development agents
- Verifying work aligns with platform plans and requirements
- Checking for scope creep or missing features
- Validating cross-repository integrations
- Ensuring journal-based traceability

## Three-Layer Review Process

### LAYER 1: Plan Verification

**Step 1: Retrieve implementation plan**

1. Get the original implementation plan from your platform's task system
2. Note: If an orchestrator agent adapted the plan, also check `{{workspaceRoot}}/work_packages/{role}/{task_id}_*_workpackage.md`

**Step 2: Read agent journal**

1. Read `{{workspaceRoot}}/journals/{role}/{task_id}_*_journal.md`
2. Extract:
   - What was implemented
   - Which plan items were addressed
   - What files were modified
   - What decisions were made

**Step 3: Compare plan vs journal**

Create comparison checklist:

```markdown
## Plan Compliance

### Planned Features
- ✓ Feature X from plan → Journal entry: "Implemented X in file.ts:42"
- ✓ Feature Y from plan → Journal entry: "Completed Y with tests in test.ts:15"
- ✗ Feature Z from plan → **Missing from journal** (not implemented)
- ⚠ Feature W in journal → **Not in original plan** (scope creep?)

### Assessment
- Planned items completed: X/Y (percentage)
- Missing items: {list}
- Extra items (scope creep): {list}
```

**If discrepancies found:**
- Flag missing planned items
- Question extra unplanned items
- Ask agent or orchestrator for clarification

### LAYER 2: Requirements Verification

**Step 4: Gather requirements and acceptance criteria**

1. **REQUIRED SUB-SKILL:** Use `context-gather` to gather:
   - User story acceptance criteria
   - Functional requirements
   - Technical requirements
   - Test coverage expectations

**Step 5: Build verification checklist**

Create checklist from gathered context:

```markdown
## Requirements Compliance

### Acceptance Criteria
- ✓ AC-1: "Users can log in with email and password" → Verified in src/auth.ts:15-42
- ✓ AC-2: "Invalid credentials show error message" → Verified in src/auth.ts:44-50
- ✗ AC-3: "Remember me option available" → **Not found in implementation**

### Functional Requirements
- ✓ FR-15: "Email validation required" → Verified in src/validators.ts:8
- ⚠ FR-16: "Password minimum 8 characters" → **Only checking 6 characters**

### Technical Requirements
- ✓ TR-22: "JWT token-based authentication" → Verified in src/jwt.ts
- ✓ TR-23: "HTTPS only for login endpoint" → Verified in middleware

### Test Coverage
- ✓ Test scenario 1: Valid login → test_auth.ts:10-25
- ✗ Test scenario 2: Invalid login → **Missing test**
- Expected coverage: 80% → Actual: 65% ⚠
```

**Step 6: Verify implementation against checklist**

For each requirement:
1. Search codebase for implementation
2. Read relevant code sections
3. Mark as ✓ (implemented), ✗ (missing), or ⚠ (partial/incorrect)

### LAYER 3: Cross-Repository Integration

**Step 7: Identify integration points**

If the feature integrates with other services:

1. Check journal for mentioned integrations
2. List API endpoints called
3. List shared data models
4. Note cross-repository dependencies

**Step 8: Verify integration contracts**

For each integration point:

1. Use your platform's code tools to access other repositories:
   - `mcp__{{Platform}}__Code_list_repositories`
   - `mcp__{{Platform}}__Code_search` for API definitions
   - `mcp__{{Platform}}__Code_cat` to read specific files

2. Verify:
   - API endpoints exist and match signatures
   - Data models are compatible
   - Error handling is consistent
   - Authentication/authorization is correct

Example verification:

```markdown
## Integration Verification

### Integration 1: User Service API
- **Endpoint**: POST /api/users/authenticate
- **Contract in other repo**: ✓ Found in user-service/api/auth.ts:42
- **Signature match**: ✓ Accepts {email, password}, returns {token, userId}
- **Error codes**: ✓ 401 (Invalid credentials), 500 (Server error)
- **Implementation alignment**: ✓ Matches contract

### Integration 2: Notification Service
- **Endpoint**: POST /api/notifications/login
- **Contract in other repo**: ⚠ Found but deprecated (see notification-service/api/DEPRECATED.md)
- **Issue**: **Using deprecated endpoint** - should use /api/events/login instead
```

## Review Report Format

After completing all three layers, generate comprehensive review report:

```markdown
# Code Review Report: {Task ID}

**Reviewer**: {Your agent name}
**Review Date**: {Date}
**Implementation Agent**: {Agent that did the work}
**User Story**: {ID and title}

---

## Executive Summary

{Brief 2-3 sentence summary of review findings}

**Overall Status**: ✓ Approved / ⚠ Approved with comments / ✗ Changes requested

---

## Layer 1: Plan Verification

### Plan Compliance: X% ({completed}/{total} items)

✓ **Implemented as planned**:
- Feature X
- Feature Y

✗ **Missing from plan**:
- Feature Z (required for AC-3)

⚠ **Scope creep detected**:
- Extra feature W (not in original plan)

### Journal Quality
- ✓ Comprehensive documentation
- ✓ Clear traceability
- ✗ Missing: {what's missing}

---

## Layer 2: Requirements Verification

### Acceptance Criteria: X% ({met}/{total} criteria)

✓ **Met**:
- AC-1: Users can log in
- AC-2: Error messages shown

✗ **Not met**:
- AC-3: Remember me option missing

### Functional Requirements: X% ({met}/{total} requirements)

⚠ **Issues found**:
- FR-16: Password validation too weak (6 chars instead of 8)

### Technical Requirements: X% ({met}/{total} requirements)

✓ **All technical requirements met**

### Test Coverage: X% (target: Y%)

⚠ **Below target**:
- Missing tests for invalid login
- Edge cases not covered

---

## Layer 3: Integration Verification

### Integration Points Verified: X/{total}

✓ **User Service API**: Correctly integrated
⚠ **Notification Service**: Using deprecated endpoint

### Recommendations:
- Update notification integration to use /api/events/login

---

## Code Quality Observations

### Strengths:
- Clean, readable code
- Good error handling
- Proper TypeScript types

### Areas for Improvement:
- Add input validation for edge cases
- Extract magic numbers to constants
- Add JSDoc comments for public APIs

---

## Security & Performance Notes

### Security:
- ✓ Input sanitization present
- ✓ HTTPS enforced
- ⚠ Consider rate limiting for login endpoint

### Performance:
- ✓ No obvious bottlenecks
- Suggestion: Cache JWT validation

---

## Action Items

**Must fix** (blocking):
1. Implement AC-3: Remember me option
2. Fix FR-16: Password validation to 8 characters minimum
3. Add missing tests for invalid login

**Should fix** (recommended):
4. Update notification service integration
5. Add rate limiting
6. Improve test coverage to 80%

**Nice to have**:
7. Add JSDoc comments
8. Extract magic numbers

---

## Verdict

**Status**: ⚠ Approved with required changes

**Reasoning**: Implementation is solid but missing key acceptance criteria. Must address items 1-3 before merge.

**Next Steps**:
1. Agent addresses action items
2. Updates journal with changes
3. Re-review after fixes
```

## Best Practices

### DO:
- ✓ Always check all three layers (plan, requirements, integration)
- ✓ Verify journal entries match actual code
- ✓ Use platform tools for cross-repo verification
- ✓ Provide specific, actionable feedback
- ✓ Distinguish between "must fix" and "nice to have"
- ✓ Be thorough but constructive

### DON'T:
- ✗ Skip journal verification (breaks traceability)
- ✗ Assume integrations work without checking
- ✗ Give vague feedback like "improve code quality"
- ✗ Approve work that doesn't meet acceptance criteria
- ✗ Focus only on code style (prioritize functionality)
- ✗ Be overly critical without providing solutions

## Integration with Workflow

This review skill integrates with:

1. **TDD Workflow**
   - Verifies tests align with acceptance criteria
   - Checks test coverage expectations
   - Validates RED→GREEN→REFACTOR→JOURNAL cycle

2. **Orchestrator Agent**
   - Reports review findings
   - Flags plan deviations for orchestrator awareness
   - Suggests when re-planning is needed

3. **Development Agents**
   - Provides actionable feedback
   - Identifies gaps for agent to address
   - Validates journal quality

4. **Platform Integration**
   - Uses platform tools for requirement verification
   - Accesses cross-repository code
   - Links findings back to user stories

## Customization

To adapt this skill to your platform:

1. **Update path patterns**: Configure `workspaceRoot` in marketplace.json
2. **Adjust verification checklist**: Match your requirements format
3. **Configure platform tools**: Ensure cross-repo access is configured
4. **Customize report format**: Match your review standards
5. **Define approval criteria**: Set thresholds for pass/fail

## Error Handling

**If journal not found:**
- Warn that traceability is broken
- Proceed with plan→code verification only
- Recommend agent updates journal

**If requirements unclear:**
- Ask user for clarification
- Document assumptions in review report
- Flag for orchestrator attention

**If cross-repo access fails:**
- Note which integrations couldn't be verified
- Mark as "Verification pending" in report
- Recommend manual integration testing

## Success Criteria

A complete code review includes:
- ✓ All three layers verified (plan, requirements, integration)
- ✓ Comprehensive review report generated
- ✓ Specific action items identified
- ✓ Clear approval/rejection verdict
- ✓ Traceability maintained (plan→journal→code)
- ✓ Cross-repository contracts validated
- ✓ Constructive, actionable feedback provided
