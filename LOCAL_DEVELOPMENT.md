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
2. **Use local files**: Copy directly from `templates/` directory in your repo
3. **Fast iteration**: Instant testing of template changes

### Important Notes

**Current Limitation**: The `--local` mode copies files from the `templates/` directory, which contains raw template files. It does NOT create the full `.specify/` directory structure that the official releases have. This is sufficient for testing template content changes, but not for testing the full project structure.

**Recommended Workflow**:

1. Make changes to template files in `templates/` directory
2. Test with `uv run specify init --local`
3. Verify template content changes work as expected
4. For full structure testing, use a proper release or manually create the structure

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
- ✅ Quick iteration on content
- ✅ Verifying template syntax
- ❌ Testing full .specify/ directory structure
- ❌ Testing release packaging
- ❌ Production use

### Production Use

For production, always use the official release (default behavior):

```bash
specify init my-project --ai copilot
```

This downloads the properly packaged and tested template from GitHub releases.
