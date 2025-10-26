# Local Development Guide

## Testing Template Changes Locally

When developing and testing changes to spec-kit-operations, you don't want to push to GitHub and create releases just to test the `specify init` command. Use the `--local` flag to use your local templates directly.

### Quick Start

```bash
# From the spec-kit-operations repo directory

# Run specify init with local templates
uv run specify init test-project --ai copilot --local

# Or set environment variable for persistent local mode
export SPECIFY_LOCAL_DEV=1
uv run specify init test-project --ai copilot
```

### How It Works

The `--local` flag (or `SPECIFY_LOCAL_DEV=1` environment variable) tells `specify init` to:

1. **Skip GitHub download**: No API calls to GitHub releases
2. **Use local files**: Copy directly from your repo's `templates/`, `memory/`, and `scripts/` directories
3. **Create proper structure**: Builds the full `.specify/` directory structure
4. **Fast iteration**: Instant testing of template changes

### Directory Structure Created

```
test-project/
├── .specify/
│   ├── templates/        # Your templates/ directory
│   ├── memory/           # Your memory/ directory (organization-context.md, etc.)
│   └── scripts/          # Your scripts/ directory (bash & PowerShell)
├── .github/prompts/      # Copilot commands (or appropriate agent folder)
└── .vscode/settings.json # VS Code settings
```

### Editing Organization Context

After initializing with `--local`, you can edit the organization context:

```bash
# Edit the organization context file
code test-project/.specify/memory/organization-context.md
```

This file is crucial for the spec-driven operations workflow and should be populated with your organization's actual information before using the planning commands.

### Important Notes

**Full Structure**: Unlike earlier versions, the `--local` mode now creates the complete `.specify/` directory structure, identical to what GitHub releases provide.

**Recommended Workflow**:

1. Make changes to files in `templates/`, `memory/`, or `scripts/` directories
2. Test with `uv run specify init test-project --local`
3. Verify all changes work as expected
4. The project structure will be identical to production releases

### Examples

```bash
# Initialize in current directory with local templates
uv run specify init --here --ai claude --local

# Create new project in subdirectory
uv run specify init my-test --ai copilot --local --no-git

# Use environment variable (persists in shell session)
export SPECIFY_LOCAL_DEV=1
uv run specify init test1 --ai copilot
uv run specify init test2 --ai claude
uv run specify init test3 --ai gemini
```

### When to Use

- ✅ Testing changes to template markdown files
- ✅ Testing command prompt changes
- ✅ Testing memory files (organization-context.md, constitution.md)
- ✅ Testing script changes
- ✅ Quick iteration on content
- ✅ Verifying complete project structure
- ✅ Full workflow testing without releases
- ❌ Production use

### Production Use

For production, always use the official release (default behavior):

```bash
specify init my-project --ai copilot
```

This downloads the properly packaged and tested template from GitHub releases.
