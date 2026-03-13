# Spec: jira-board --intake Flag & Hook Integration

## Feature Summary

Add intake-file-driven pruning to the `/jira-board` command. Supports two modes:
1. **Standalone**: `--intake <file>` flag reads a YAML/JSON file and prunes board tasks
2. **Hook**: When invoked by `org-generator`, intake params are passed via `$INTAKE_FILE` env var

## Changes to jira-board.md

### 1. New Flag: `--intake`

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| `--intake` | `-i` | `null` | Path to YAML/JSON intake file for task pruning |

### 2. Intake File Schema

```yaml
intake:
  project_name: "<name>"
  team_scope: ["<team-a>", "<team-b>"]   # only these teams get tasks
  project_type: "<type>"                   # maps to template
  include_phases: ["phase-1", "phase-2"]   # only these phases get tasks
  exclude_labels: ["optional", "stretch"]  # tasks with these labels are pruned
  custom_fields: {}                        # pass-through for template variables
```

### 3. Hook Integration

When org-generator invokes jira-board:
- `$INTAKE_FILE` env var is set to the intake file path
- If `--intake` flag is also provided, flag takes precedence
- Resolution order: `--intake` flag > `$INTAKE_FILE` env var > no pruning

### 4. Pruning Logic (Phase 1.5 — between Discovery and Parallel Execution)

1. Load intake file (from flag or env var)
2. Parse YAML/JSON intake
3. Filter template tasks:
   - Remove tasks not matching `team_scope`
   - Remove tasks not in `include_phases`
   - Remove tasks with labels in `exclude_labels`
4. Log pruned count: "Pruned N tasks based on intake parameters"

### 5. Auto-Hints Updates

Add to argument-hints frontmatter:
- `"--intake <file>: YAML/JSON intake file for task pruning (standalone or hook mode)"`
- `"Env: $INTAKE_FILE — auto-set when invoked by org-generator"`
- `"Intake filters: team_scope, include_phases, exclude_labels"`

### 6. ASCII File Tree Output (Phase 5 addition)

After board creation, output an ASCII tree of all created artifacts:
```
jira-board/
├── board: <board-name> (ID: <board-id>)
│   ├── columns/
│   │   ├── Backlog
│   │   ├── In Progress
│   │   ├── Review
│   │   └── Done
│   ├── issue-types/
│   │   ├── Task
│   │   ├── Story
│   │   └── Bug
│   ├── workflows/
│   │   └── <N> transitions
│   └── filters/
│       └── <filter-names>
└── intake-pruning: <N> tasks pruned
```

## Constraints

- All `{{ File Location }}` placeholders MUST be preserved
- Backward compatible: no intake = current behavior unchanged
- Intake file errors halt with clear message, no partial board creation
