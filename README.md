# stackmd

`stackmd` builds a single markdown "pack" from a local repository so you can paste or upload it into ChatGPT, Claude, or other LLM chat interfaces that do **not** have direct access to your filesystem.

It is designed for portability, not deep codebase-scale navigation.

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

It also supports a docs-only mode for packaging documentation files only.

Default outputs:

- STACKMD-<FOLDER>-CODEBASE.md
- STACKMD-<FOLDER>-DOCS.md

## Important limitation

This does NOT scale to large codebases.

Use context engineering instead:
- split files
- guide the agent
- expand context incrementally

## Install + Run

### macOS

mkdir -p ~/bin  
cp stackmd_portable.py ~/bin/stackmd  
chmod +x ~/bin/stackmd  

Add to PATH:

echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc  
source ~/.zshrc  

Run anywhere:

stackmd  
stackmd --docs-only  

### Windows

Create folder:
C:\Users\YourName\bin

Add file:
stackmd_portable.py

Create stackmd.cmd:

@echo off
py "C:\Users\YourName\bin\stackmd_portable.py" %*

Add to PATH (PowerShell):

setx PATH "$env:PATH;C:\Users\YourName\bin"

Run:

stackmd  
stackmd --docs-only  

## Usage

stackmd  
stackmd ../repo  
stackmd --docs-only  
stackmd --extensions .py,.ts  
stackmd -o output.md  

## Output Example

Created /repo/STACKMD-REPO-CODEBASE.md with 42 files

## .gitignore

STACKMD*

## Use Case

Upload output into ChatGPT / Claude when they cannot access your repo.

