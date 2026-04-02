# stackmd

`stackmd` builds a single markdown "pack" from a local repository so you can paste or upload it into ChatGPT, Claude, or other LLM chat interfaces that do **not** have direct access to your filesystem.

It is designed for portability first, not deep codebase-scale navigation.

## Small repo description

Generate a single markdown pack of a codebase or docs folder for LLM chat interfaces like ChatGPT and Claude when they do not have filesystem access.

## What it does

The script scans a directory and produces one markdown file containing:

- a manifest
- a reading guide
- a directory tree
- a file index
- selected file contents

It tries to stay practical by:

- skipping junk directories and low-signal assets
- detecting binary files
- classifying files by role
- choosing per-file modes:
  - `full`
  - `chunked`
  - `summary`
- keeping output within a practical size budget
- reducing whitespace by default to save tokens
- supporting ai-digest-style ignore and minify files

It also supports a docs-only mode for packaging documentation files only.

Default outputs:

- `STACKMD-<FOLDER>-CODEBASE.md`
- `STACKMD-<FOLDER>-DOCS.md`

These names stay the default unless you explicitly override them with `-o` or `--output`.

## Important limitation

This does **not** scale well to very large codebases.

The better approach for large systems is context engineering:

- split files
- guide the agent to the right files
- expand context incrementally
- avoid trying to dump an entire monorepo into one chat unless absolutely necessary

`stackmd` is mainly useful for:

- small to medium repositories
- docs folders
- targeted subdirectories
- quick codebase snapshots for LLM review in chat UIs

## Features

### Core packaging

- single-file markdown output
- manifest and reading guide
- directory tree
- file index
- full / chunked / summary file modes
- docs-only mode
- extension filtering
- hidden file inclusion toggle
- output size budgeting

### Token reduction

Whitespace reduction is enabled by default.

This helps reduce token usage when pasting into LLM chat interfaces.

Use this flag to disable it:

```bash
stackmd --no-whitespace-removal
```

### Ignore patterns

`stackmd` supports custom ignore patterns through `.aidigestignore` in the repo root.

This works similarly to `.gitignore` and lets you exclude additional files or directories from the pack.

Example:

```gitignore
dist/
build/
coverage/
*.log
.large-cache/
```

### Minify patterns

`stackmd` supports custom minify patterns through `.aidigestminify` in the repo root.

Files matching these patterns are kept in the output, but their full contents are replaced with a placeholder so the LLM still knows the file exists without spending the full token budget on it.

Example:

```gitignore
*.min.js
*.min.css
dist/*
build/*
*.db
*.sqlite
*.csv
*.json
```

### Output file visibility report

You can print the included output files and their size statistics:

```bash
stackmd --show-output-files
```

Sort by size:

```bash
stackmd --show-output-files sort
```

### Watch mode

You can rebuild automatically whenever relevant files change:

```bash
stackmd --watch
```

This is useful when iterating on a repository and regenerating the pack repeatedly.

### Default ignore behavior

Default ignore rules are enabled by default.

To disable them:

```bash
stackmd --no-default-ignores
```

## Install + Run

### macOS

Create a local bin directory and copy the script:

```bash
mkdir -p ~/bin
cp stackmd_portable.py ~/bin/stackmd
chmod +x ~/bin/stackmd
```

Add it to your `PATH`:

```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Run anywhere:

```bash
stackmd
stackmd --docs-only
```

### Windows

Create a folder such as:

```text
C:\Users\YourName\bin
```

Put the script there as:

```text
C:\Users\YourName\bin\stackmd_portable.py
```

Create `stackmd.cmd` in the same folder:

```bat
@echo off
py "C:\Users\YourName\bin\stackmd_portable.py" %*
```

Add the folder to your `PATH` in PowerShell:

```powershell
setx PATH "$env:PATH;C:\Users\YourName\bin"
```

Then open a new terminal and run:

```bash
stackmd
stackmd --docs-only
```

## Usage

Basic usage:

```bash
stackmd
stackmd ../repo
stackmd --docs-only
stackmd -o output.md
```

Filter by extensions:

```bash
stackmd --extensions .py,.ts,.tsx,.md
```

Disable whitespace reduction:

```bash
stackmd --no-whitespace-removal
```

Show included output files:

```bash
stackmd --show-output-files
stackmd --show-output-files sort
```

Use watch mode:

```bash
stackmd --watch
```

Disable default ignores:

```bash
stackmd --no-default-ignores
```

Include hidden files:

```bash
stackmd --include-hidden
```

Adjust output budget:

```bash
stackmd --max-output-chars 1200000
stackmd --chunk-char-limit 16000
```

## CLI reference

```bash
stackmd [directory]
stackmd [directory] -o output.md
stackmd [directory] --docs-only
stackmd [directory] --extensions .py,.ts
stackmd [directory] --include-hidden
stackmd [directory] --max-output-chars 1500000
stackmd [directory] --full-cutoff 12000
stackmd [directory] --chunk-cutoff 80000
stackmd [directory] --chunk-char-limit 18000
stackmd [directory] --show-output-files
stackmd [directory] --show-output-files sort
stackmd [directory] --watch
stackmd [directory] --no-default-ignores
stackmd [directory] --no-whitespace-removal
```

## Output example

```text
Created /repo/STACKMD-REPO-CODEBASE.md with 42 included files, 87 skipped items, and 12844 included lines
```

## Recommended workflow

For best results:

1. Run `stackmd` on a specific repo or subdirectory, not your entire machine.
2. Use `.aidigestignore` to exclude noise.
3. Use `.aidigestminify` for large generated or low-signal files.
4. Start with the reading guide and file index in the generated output.
5. For very large codebases, package only the specific folders relevant to your question.

## .gitignore

If you do not want generated outputs committed:

```gitignore
STACKMD*
```

## Use case

Use `stackmd` when you want to:

- upload a repo snapshot into ChatGPT or Claude
- paste a compact codebase pack into a chat
- share docs or a targeted source tree with an LLM
- generate a portable markdown view of a local repository when the model cannot access your filesystem

