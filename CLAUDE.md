# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A tutorial collection teaching Claude Code features through 10 numbered lesson modules (`01-slash-commands/` through `10-cli/`). Each module has a `README.md` (the lesson) plus example files (skills, subagents, hooks, etc.) that users copy into their own projects.

The repo also contains two interactive Claude Code skills in `.claude/skills/`:
- `self-assessment` — runs a quiz to determine user level and generate a learning path
- `lesson-quiz` — tests understanding of a specific lesson

## Development Commands

### Python (EPUB builder + validation scripts)

```bash
# Setup
uv venv && source .venv/bin/activate
uv pip install -r scripts/requirements-dev.txt
pre-commit install

# Run all checks (same as CI)
pre-commit run --all-files

# Run tests
pytest scripts/tests/ -v

# Build EPUB
uv run scripts/build_epub.py --verbose
```

### Markdown checks (run individually)

```bash
markdownlint '**/*.md' --ignore node_modules --ignore .venv --config .markdownlint.json
python scripts/check_cross_references.py
python scripts/check_mermaid.py
python scripts/check_links.py
```

### Pre-commit hooks (run automatically on commit)

| Hook | What it checks |
|------|----------------|
| `markdown-lint` | Markdown formatting |
| `cross-references` | Relative links and anchors |
| `mermaid-syntax` | All `mermaid` blocks parse correctly |
| `link-check` | External URLs are reachable |
| `build-epub` | EPUB generates without errors |

## Content Architecture

### Lesson modules (`01-slash-commands/` … `10-cli/`)

Each module follows this structure:
- `README.md` — the lesson (overview → architecture diagram → examples → best practices → troubleshooting)
- Example files — templates users copy into their own projects (slash commands, SKILL.md files, subagent `.md`, hook configs, etc.)

The numbered prefix reflects the learning path order (beginner → advanced).

### Skills (`.claude/skills/`)

Skills are Claude Code capabilities loaded into this session. They follow the SKILL.md format with YAML frontmatter. The `self-assessment` and `lesson-quiz` skills are the interactive entry points for learners using this repo with Claude Code.

### Python scripts (`scripts/`)

Pure utility scripts — EPUB builder and four CI validation scripts. Tests live in `scripts/tests/`. `pyproject.toml` in `scripts/` configures pytest, ruff, and bandit.

## Content Conventions (from STYLE_GUIDE.md)

- **File names**: kebab-case for all files/folders; UPPER_CASE for top-level docs (`CATALOG.md`, `CONTRIBUTING.md`, etc.)
- **Lesson README structure**: H1 title → overview → quick-reference table → Mermaid architecture diagram → detailed H2 sections → examples → best practices (Do/Don't table) → troubleshooting → related guides
- **Callouts**: Use `> **Note**:`, `> **Tip**:`, `> **Warning**:`, `> **Important**:` blockquote patterns
- **Lists**: Dashes (`-`) with 2-space nesting; numbered lists for sequential steps only
- **Headings**: Sentence case, one H1 per doc, never skip levels
- **SKILL.md frontmatter**: Required fields are `name` and `description`; `description` controls auto-invocation behavior

## Adding Content

- **New slash command**: Add `.md` to `01-slash-commands/`, update `01-slash-commands/README.md`
- **New skill**: Create `03-skills/<name>/SKILL.md` (plus optional `scripts/`, `templates/`, `references/` subdirs), update `03-skills/README.md`
- **New subagent**: Add `.md` to `04-subagents/`, update `04-subagents/README.md`
- **Branch naming**: `add/feature-name`, `fix/issue-description`, `docs/improvement-area`
