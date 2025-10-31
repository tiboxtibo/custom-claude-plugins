---
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Task
argument-hint: "[--step-by-step] [--auto-pause] [--verbose]"
description: "Modalità interattiva con esecuzione step-by-step e controllo utente"
---

# Toduba Interactive Mode - Esecuzione Interattiva 🎮

## Obiettivo
Fornire un'esecuzione controllata step-by-step con possibilità di pause, resume, undo e controllo completo del flusso.

## Argomenti
- `--step-by-step`: Richiede conferma ad ogni step
- `--auto-pause`: Pausa automatica su warning/error
- `--verbose`: Output dettagliato per ogni operazione
- `--checkpoint`: Crea checkpoint ad ogni step major

Argomenti ricevuti: $ARGUMENTS

## Interactive Session Manager

```typescript
class InteractiveSession {
  private steps: Step[] = [];
  private currentStep: number = 0;
  private paused: boolean = false;
  private history: StepResult[] = [];
  private checkpoints: Checkpoint[] = [];

  async start(task: Task) {
    console.log('🎮 TODUBA INTERACTIVE MODE');
    console.log('━━━━━━━━━━━━━━━━━━━━━━━━━');
    console.log('');
    console.log('Controls:');
    console.log('  [Enter] - Continue');
    console.log('  [p]     - Pause');
    console.log('  [s]     - Skip step');
    console.log('  [u]     - Undo last');
    console.log('  [r]     - Resume all');
    console.log('  [q]     - Quit');
    console.log('');

    this.initializeSteps(task);
    await this.executeInteractive();
  }
}
```

## Step-by-Step Execution Flow

### Visual Progress Display
```
┌─────────────────────────────────────────────────────────┐
│ 🎮 INTERACTIVE EXECUTION                               │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ Task: Create user authentication API                   │
│ Mode: Step-by-step                                    │
│ Progress: [████████░░░░░░░░] 40% (4/10 steps)        │
│                                                         │
│ ┌─ Current Step ──────────────────────────────────┐   │
│ │ Step 4: Creating user model                     │   │
│ │ Agent: toduba-backend-engineer                  │   │
│ │ Action: Write file models/User.js               │   │
│ │ Status: ⏸️ Awaiting confirmation                │   │
│ └─────────────────────────────────────────────────┘   │
│                                                         │
│ Previous: ✅ Database connection setup              │
│ Next: Create authentication middleware              │
│                                                         │
│ [Enter] Continue | [p] Pause | [u] Undo | [q] Quit   │
└─────────────────────────────────────────────────────────┘
```

### Step Structure
```typescript
interface Step {
  id: string;
  name: string;
  description: string;
  agent: string;
  action: Action;
  dependencies: string[];
  canUndo: boolean;
  critical: boolean;
  estimatedTime: number;
}

interface Action {
  type: 'create' | 'modify' | 'delete' | 'execute';
  target: string;
  details: any;
}
```

## Interactive Controls Implementation

### Pause/Resume System
```javascript
const handleUserInput = async (input) => {
  switch(input.toLowerCase()) {
    case 'p':
    case 'pause':
      await pauseExecution();
      break;

    case 'r':
    case 'resume':
      await resumeExecution();
      break;

    case 's':
    case 'skip':
      await skipCurrentStep();
      break;

    case 'u':
    case 'undo':
      await undoLastStep();
      break;

    case 'q':
    case 'quit':
      await quitInteractive();
      break;

    case '':
    case 'enter':
      await continueExecution();
      break;

    case 'h':
    case 'help':
      showInteractiveHelp();
      break;

    default:
      console.log('Unknown command. Press [h] for help.');
  }
};
```

### Undo Mechanism
```javascript
async undoLastStep() {
  if (this.history.length === 0) {
    console.log('❌ No steps to undo');
    return;
  }

  const lastStep = this.history.pop();
  console.log(`↩️ Undoing: ${lastStep.step.name}`);

  // Show what will be undone
  console.log('');
  console.log('This will revert:');
  lastStep.changes.forEach(change => {
    console.log(`  • ${change.type}: ${change.file}`);
  });

  const confirm = await promptUser('Confirm undo? (y/n): ');

  if (confirm === 'y') {
    // Revert changes
    await this.revertStep(lastStep);
    this.currentStep--;
    console.log('✅ Step undone successfully');
  } else {
    this.history.push(lastStep);
    console.log('❌ Undo cancelled');
  }
}
```

## Checkpoint System

```javascript
class CheckpointManager {
  async createCheckpoint(name: string, metadata: any) {
    const checkpoint = {
      id: `checkpoint-${Date.now()}`,
      name,
      timestamp: new Date(),
      step: this.currentStep,
      files: await this.captureFileState(),
      metadata
    };

    // Save current state
    await this.saveCheckpoint(checkpoint);

    console.log(`💾 Checkpoint created: ${checkpoint.name}`);
    return checkpoint.id;
  }

  async restoreCheckpoint(checkpointId: string) {
    console.log(`🔄 Restoring checkpoint: ${checkpointId}`);

    const checkpoint = await this.loadCheckpoint(checkpointId);

    // Show changes
    console.log('');
    console.log('This will restore to:');
    console.log(`  Step: ${checkpoint.step}`);
    console.log(`  Time: ${checkpoint.timestamp}`);
    console.log(`  Files: ${checkpoint.files.length}`);

    const confirm = await promptUser('Proceed? (y/n): ');

    if (confirm === 'y') {
      await this.applyCheckpoint(checkpoint);
      console.log('✅ Checkpoint restored');
    }
  }
}
```

## Step Details Preview

```javascript
async previewStep(step: Step) {
  console.clear();
  console.log('┌─────────────────────────────────────────┐');
  console.log('│ 📋 STEP PREVIEW                         │');
  console.log('├─────────────────────────────────────────┤');
  console.log(`│ Step ${step.id}: ${step.name}`);
  console.log('├─────────────────────────────────────────┤');
  console.log('│');
  console.log(`│ Description:`);
  console.log(`│   ${step.description}`);
  console.log('│');
  console.log(`│ Will be executed by:`);
  console.log(`│   🤖 ${step.agent}`);
  console.log('│');
  console.log(`│ Actions to perform:`);

  step.actions.forEach(action => {
    console.log(`│   • ${action.type}: ${action.target}`);
  });

  console.log('│');
  console.log(`│ Estimated time: ${step.estimatedTime}s`);
  console.log('│');

  if (step.critical) {
    console.log('│ ⚠️  CRITICAL STEP - Cannot be skipped');
  }

  console.log('│');
  console.log('└─────────────────────────────────────────┘');
  console.log('');

  return await promptUser('[Enter] Execute | [s] Skip | [m] Modify: ');
}
```

## Breakpoint System

```javascript
class BreakpointManager {
  private breakpoints: Breakpoint[] = [];

  addBreakpoint(condition: string | Function) {
    this.breakpoints.push({
      id: `bp-${Date.now()}`,
      condition,
      hits: 0
    });
  }

  async checkBreakpoints(context: ExecutionContext) {
    for (const bp of this.breakpoints) {
      if (await this.evaluateBreakpoint(bp, context)) {
        console.log(`🔴 Breakpoint hit: ${bp.id}`);
        await this.handleBreakpoint(bp, context);
      }
    }
  }

  async handleBreakpoint(bp: Breakpoint, context: ExecutionContext) {
    console.log('');
    console.log('━━━ BREAKPOINT ━━━');
    console.log(`Location: ${context.step.name}`);
    console.log(`Condition: ${bp.condition}`);
    console.log(`Hits: ${++bp.hits}`);
    console.log('');

    // Show context
    console.log('Context:');
    console.log(JSON.stringify(context.variables, null, 2));

    // Interactive debugger
    let debugging = true;
    while (debugging) {
      const cmd = await promptUser('debug> ');

      switch(cmd) {
        case 'c':
        case 'continue':
          debugging = false;
          break;

        case 'i':
        case 'inspect':
          await this.inspectContext(context);
          break;

        case 'n':
        case 'next':
          await this.stepOver();
          debugging = false;
          break;

        case 'q':
        case 'quit':
          process.exit(0);
      }
    }
  }
}
```

## Modification Mode

```javascript
async modifyStep(step: Step) {
  console.log('✏️ MODIFY STEP');
  console.log('━━━━━━━━━━━━━━');
  console.log('');
  console.log('Current configuration:');
  console.log(JSON.stringify(step, null, 2));
  console.log('');
  console.log('What would you like to modify?');
  console.log('1. Change target files');
  console.log('2. Modify parameters');
  console.log('3. Change agent assignment');
  console.log('4. Skip this step');
  console.log('5. Cancel modification');

  const choice = await promptUser('Choice (1-5): ');

  switch(choice) {
    case '1':
      step.action.target = await promptUser('New target: ');
      break;
    case '2':
      await this.modifyParameters(step);
      break;
    case '3':
      step.agent = await this.selectAgent();
      break;
    case '4':
      step.skip = true;
      break;
  }

  console.log('✅ Step modified');
  return step;
}
```

## Watch Mode Integration

```javascript
class WatchModeIntegration {
  async enableWatchMode() {
    console.log('👁️ Watch mode enabled');
    console.log('Files will be monitored for changes');

    const watcher = chokidar.watch('.', {
      ignored: /node_modules|\.git/,
      persistent: true
    });

    watcher.on('change', async (path) => {
      if (this.isPaused) return;

      console.log(`\n📝 File changed: ${path}`);
      console.log('Options:');
      console.log('[r] Re-run current step');
      console.log('[c] Continue anyway');
      console.log('[p] Pause to investigate');

      const action = await promptUser('Action: ');
      await this.handleFileChange(action, path);
    });
  }
}
```

## Summary Report

```markdown
## 📊 Interactive Session Summary

**Session ID**: interactive-20241031-145632
**Duration**: 15 minutes 23 seconds
**Mode**: Step-by-step with checkpoints

### Execution Statistics
| Metric | Value |
|--------|-------|
| Total Steps | 10 |
| Completed | 8 |
| Skipped | 1 |
| Undone | 1 |
| Breakpoints Hit | 3 |
| Checkpoints | 4 |

### Step Timeline
```
1. ✅ Initialize project structure (0:23)
2. ✅ Setup database connection (1:45)
3. ✅ Create user model (2:12)
4. ↩️ UNDONE: Create auth middleware
5. ✅ Create auth middleware v2 (4:33)
6. ⏭️ SKIPPED: Add logging
7. ✅ Create API endpoints (6:21)
8. ✅ Add validation (8:45)
9. ✅ Write tests (10:12)
10. ✅ Update documentation (11:54)
```

### User Interactions
- Pauses: 2
- Modifications: 3
- Undo operations: 1
- Breakpoint inspections: 3

### Files Modified
- Created: 12 files
- Modified: 8 files
- Deleted: 0 files

### Checkpoints Available
1. `checkpoint-1698765392000` - After database setup
2. `checkpoint-1698765512000` - After auth implementation
3. `checkpoint-1698765634000` - After API creation
4. `checkpoint-1698765756000` - Final state

### Recommendations
- Consider automating step 6 (skipped frequently)
- Breakpoint at auth middleware hit multiple times
- Average pause duration: 45 seconds
```

## Quick Commands

During interactive execution:

```
┌────────────────────────────────┐
│ ⌨️ QUICK COMMANDS             │
├────────────────────────────────┤
│ Enter   - Continue             │
│ p       - Pause                │
│ r       - Resume               │
│ s       - Skip step            │
│ u       - Undo last            │
│ m       - Modify step          │
│ b       - Set breakpoint       │
│ c       - Create checkpoint    │
│ l       - List checkpoints     │
│ i       - Inspect context      │
│ h       - Help                 │
│ q       - Quit                 │
└────────────────────────────────┘
```