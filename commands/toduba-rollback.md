---
allowed-tools:
  - Bash
  - Read
  - Write
  - Glob
  - Grep
argument-hint: "[--last] [--steps <n>] [--to <commit>] [--dry-run] [--force]"
description: "Sistema di rollback con snapshot automatici per annullare modifiche"
---

# Toduba Rollback - Sistema di Rollback Intelligente ↩️

## Obiettivo
Fornire un sistema di rollback sicuro e intelligente per annullare modifiche, con snapshot automatici prima di ogni operazione significativa.

## Argomenti
- `--last`: Rollback ultima operazione (default)
- `--steps <n>`: Rollback di N operazioni
- `--to <commit>`: Rollback a specific commit
- `--dry-run`: Mostra cosa verrebbe rollbackato senza farlo
- `--force`: Skip conferme di sicurezza
- `--list`: Mostra snapshot disponibili

Argomenti ricevuti: $ARGUMENTS

## Sistema di Snapshot

### Auto-Snapshot Before Changes
```bash
# Automaticamente creato da orchestrator prima di modifiche
create_snapshot() {
  local snapshot_id="toduba-$(date +%Y%m%d-%H%M%S)"
  local snapshot_dir=".toduba/snapshots/$snapshot_id"

  mkdir -p "$snapshot_dir"

  # Save current state
  echo "📸 Creating snapshot: $snapshot_id"

  # 1. Git state
  git diff > "$snapshot_dir/uncommitted.diff"
  git status --porcelain > "$snapshot_dir/status.txt"
  git rev-parse HEAD > "$snapshot_dir/last_commit.txt"

  # 2. File list
  find . -type f -not -path "./.git/*" -not -path "./node_modules/*" \
    > "$snapshot_dir/files.txt"

  # 3. Metadata
  cat > "$snapshot_dir/metadata.json" <<EOF
{
  "id": "$snapshot_id",
  "timestamp": "$(date -Iseconds)",
  "description": "$1",
  "files_count": $(wc -l < "$snapshot_dir/files.txt"),
  "uncommitted_changes": $(git status --porcelain | wc -l),
  "user": "$(git config user.name)",
  "operation": "$2"
}
EOF

  # 4. Create restore point
  tar czf "$snapshot_dir/backup.tar.gz" \
    --exclude=".git" \
    --exclude="node_modules" \
    --exclude=".toduba/snapshots" \
    .

  echo "✅ Snapshot created: $snapshot_id"
  return 0
}
```

## Processo di Rollback

### Fase 1: Identificazione Snapshot

```bash
identify_rollback_target() {
  local target=""

  if [[ "$ARGUMENTS" == *"--last"* ]] || [ -z "$ARGUMENTS" ]; then
    # Get last snapshot
    target=$(ls -t .toduba/snapshots | head -1)
    echo "🎯 Target: Last operation ($target)"

  elif [[ "$ARGUMENTS" == *"--steps"* ]]; then
    # Get N snapshots back
    local steps=$(echo "$ARGUMENTS" | grep -oP '(?<=--steps )\d+')
    target=$(ls -t .toduba/snapshots | sed -n "${steps}p")
    echo "🎯 Target: $steps steps back ($target)"

  elif [[ "$ARGUMENTS" == *"--to"* ]]; then
    # Rollback to specific commit
    local commit=$(echo "$ARGUMENTS" | grep -oP '(?<=--to )\w+')
    echo "🎯 Target: Git commit $commit"
    git_rollback=true
  fi

  if [ -z "$target" ] && [ "$git_rollback" != true ]; then
    echo "❌ No valid rollback target found"
    exit 1
  fi

  ROLLBACK_TARGET="$target"
}
```

### Fase 2: Pre-Rollback Analysis

```bash
analyze_rollback_impact() {
  echo ""
  echo "📊 Rollback Impact Analysis"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━"

  local snapshot_dir=".toduba/snapshots/$ROLLBACK_TARGET"

  if [ -f "$snapshot_dir/metadata.json" ]; then
    # Parse metadata
    local timestamp=$(jq -r '.timestamp' "$snapshot_dir/metadata.json")
    local description=$(jq -r '.description' "$snapshot_dir/metadata.json")
    local files_count=$(jq -r '.files_count' "$snapshot_dir/metadata.json")

    echo "📅 Snapshot: $ROLLBACK_TARGET"
    echo "🕒 Created: $timestamp"
    echo "📝 Description: $description"
    echo "📁 Files: $files_count"
  fi

  # Current vs Target comparison
  echo ""
  echo "Changes to be reverted:"
  echo "━━━━━━━━━━━━━━━━━━━━━━"

  # Show file differences
  git diff --stat HEAD "$(cat $snapshot_dir/last_commit.txt 2>/dev/null)"

  # Count changes
  local added=$(git diff --numstat HEAD "$(cat $snapshot_dir/last_commit.txt)" | wc -l)
  local modified=$(git status --porcelain | grep "^ M" | wc -l)
  local deleted=$(git status --porcelain | grep "^ D" | wc -l)

  echo ""
  echo "Summary:"
  echo "  ➕ Added: $added files"
  echo "  ✏️ Modified: $modified files"
  echo "  ❌ Deleted: $deleted files"

  if [[ "$ARGUMENTS" == *"--dry-run"* ]]; then
    echo ""
    echo "🔍 DRY RUN MODE - No changes will be made"
    exit 0
  fi
}
```

### Fase 3: Safety Checks

```bash
perform_safety_checks() {
  echo ""
  echo "🔒 Safety Checks"
  echo "━━━━━━━━━━━━━━━━━━━"

  # Check 1: Uncommitted changes
  if [ -n "$(git status --porcelain)" ]; then
    echo "⚠️  Warning: You have uncommitted changes"

    if [[ "$ARGUMENTS" != *"--force"* ]]; then
      read -p "Create backup before rollback? (Y/n): " backup_choice
      if [ "$backup_choice" != "n" ]; then
        create_snapshot "Pre-rollback backup" "manual"
      fi
    fi
  fi

  # Check 2: Running processes
  if pgrep -f "npm run dev" > /dev/null; then
    echo "⚠️  Warning: Development server is running"
    echo "   It will be stopped during rollback"
  fi

  # Check 3: Database state
  if [ -f ".toduba/db-version.txt" ]; then
    echo "⚠️  Warning: Database migrations may need reverting"
  fi

  # Final confirmation
  if [[ "$ARGUMENTS" != *"--force"* ]]; then
    echo ""
    echo "⚠️  This action cannot be undone (except by another rollback)"
    read -p "Proceed with rollback? (y/N): " confirm
    if [ "$confirm" != "y" ]; then
      echo "❌ Rollback cancelled"
      exit 0
    fi
  fi
}
```

### Fase 4: Execute Rollback

```bash
execute_rollback() {
  echo ""
  echo "🔄 Executing Rollback"
  echo "━━━━━━━━━━━━━━━━━━━━━"

  local snapshot_dir=".toduba/snapshots/$ROLLBACK_TARGET"

  # Stop any running processes
  echo "📦 Stopping running processes..."
  pkill -f "npm run dev" 2>/dev/null || true
  pkill -f "npm start" 2>/dev/null || true

  # Git rollback if specified
  if [ "$git_rollback" = true ]; then
    echo "📝 Rolling back to commit: $commit"
    git reset --hard "$commit"
  else
    # File system rollback
    echo "📁 Restoring files from snapshot..."

    # Create safety backup
    mv . .toduba/pre_rollback_$(date +%s) 2>/dev/null || true

    # Extract snapshot
    tar xzf "$snapshot_dir/backup.tar.gz" -C .

    # Restore git state
    if [ -f "$snapshot_dir/uncommitted.diff" ]; then
      git apply "$snapshot_dir/uncommitted.diff" 2>/dev/null || true
    fi
  fi

  # Post-rollback tasks
  post_rollback_tasks
}

post_rollback_tasks() {
  echo ""
  echo "🔧 Post-Rollback Tasks"
  echo "━━━━━━━━━━━━━━━━━━━━━"

  # Reinstall dependencies if package.json changed
  if git diff HEAD~1 HEAD --name-only | grep -q "package.json"; then
    echo "📦 Reinstalling dependencies..."
    npm install
  fi

  # Run migrations if needed
  if [ -f ".toduba/run-migrations.sh" ]; then
    echo "🗄️ Running database migrations..."
    ./.toduba/run-migrations.sh
  fi

  # Clear caches
  echo "🧹 Clearing caches..."
  rm -rf .cache/ dist/ build/ 2>/dev/null || true

  # Rebuild if needed
  if [ -f "package.json" ] && grep -q '"build"' package.json; then
    echo "🔨 Rebuilding project..."
    npm run build
  fi
}
```

### Fase 5: Rollback Report

```markdown
## 📋 Rollback Report

**Timestamp**: [DATE TIME]
**Rollback Type**: [snapshot/git]
**Target**: [SNAPSHOT_ID or COMMIT]

### ✅ Actions Completed
- [x] Stopped running processes
- [x] Created safety backup
- [x] Restored files from snapshot
- [x] Applied uncommitted changes
- [x] Reinstalled dependencies
- [x] Cleared caches
- [x] Rebuilt project

### 📊 Statistics
- Files restored: 156
- Dependencies updated: 3
- Cache cleared: 12MB
- Time taken: 45 seconds

### ⚠️ Manual Actions Required
1. Restart development server: `npm run dev`
2. Check database state if applicable
3. Verify application functionality
4. Review restored code changes

### 🔄 Rollback History
```
toduba-20241031-143022  ← CURRENT
toduba-20241031-140515
toduba-20241031-134208
toduba-20241031-125633
```

### 💡 Next Steps
- Test application thoroughly
- If issues persist, rollback further: `/toduba-rollback --steps 2`
- To undo this rollback: `/toduba-rollback --last`
```

## Snapshot Management

### List Available Snapshots
```bash
list_snapshots() {
  echo "📸 Available Snapshots"
  echo "━━━━━━━━━━━━━━━━━━━━━━━"

  for snapshot in .toduba/snapshots/*/metadata.json; do
    if [ -f "$snapshot" ]; then
      local id=$(jq -r '.id' "$snapshot")
      local time=$(jq -r '.timestamp' "$snapshot")
      local desc=$(jq -r '.description' "$snapshot")
      local size=$(du -sh "$(dirname "$snapshot")" | cut -f1)

      printf "%-25s %s  %6s  %s\n" "$id" "$time" "$size" "$desc"
    fi
  done

  echo ""
  echo "Total: $(ls -1 .toduba/snapshots | wc -l) snapshots"
  echo "Disk usage: $(du -sh .toduba/snapshots | cut -f1)"
}
```

### Auto-Cleanup Old Snapshots
```bash
cleanup_old_snapshots() {
  # Keep only last 20 snapshots or 7 days
  local max_age=7  # days
  local max_count=20

  echo "🧹 Cleaning old snapshots..."

  # Delete by age
  find .toduba/snapshots -type d -mtime +$max_age -exec rm -rf {} \;

  # Delete by count
  ls -t .toduba/snapshots | tail -n +$((max_count + 1)) | \
    xargs -I {} rm -rf ".toduba/snapshots/{}"

  echo "✅ Cleanup complete"
}
```

## Integration with Orchestrator

The orchestrator automatically creates snapshots before:
- Major refactoring
- Database migrations
- Dependency updates
- Bulk file operations
- Deployment preparations

```javascript
// In orchestrator work package
if (taskComplexity === 'high' || modifiedFiles > 10) {
  await createSnapshot(`Pre-${taskName}`, taskName);
}
```

## Error Recovery

```bash
handle_rollback_error() {
  echo "❌ Rollback failed!"
  echo ""
  echo "Emergency recovery options:"
  echo "1. Check .toduba/pre_rollback_* for backup"
  echo "2. Use git history: git reflog"
  echo "3. Restore from .toduba/snapshots manually"
  echo "4. Contact support with error details"

  # Save error log
  echo "$1" > .toduba/rollback_error.log
  exit 1
}
```