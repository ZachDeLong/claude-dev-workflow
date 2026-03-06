---
name: setup
description: "Use when starting work on a new project or a project without a CLAUDE.md. Scans the repo and generates a minimal, practical CLAUDE.md with stack info, commands, key paths, and conventions."
metadata:
  author: zachd
  version: "1.0.0"
---

# Project Setup

## Overview

Analyze a project repo and generate a minimal, practical CLAUDE.md. Like something a senior dev would write in 5 minutes to onboard a teammate — stack, commands, key paths, conventions. No boilerplate, no fluff.

Also recommend which Claude Code plugins to install based on the detected stack.

<HARD-GATE>
Do NOT start building features, fixing bugs, or doing any project work. Your ONLY job is to analyze the repo and generate a CLAUDE.md.
Do NOT generate a CLAUDE.md without scanning the repo first. Always read actual files before making claims about the project.
Do NOT output boilerplate sections with nothing useful in them. If there's nothing to say, skip the section.
</HARD-GATE>

## Process

### Step 1: Check for existing CLAUDE.md

Read `CLAUDE.md` in the project root if it exists.

- **If it exists and looks complete** — tell the user: "Your CLAUDE.md already looks solid. Here's what I'd add or update:" then suggest specific improvements based on the repo scan.
- **If it exists but is sparse or outdated** — offer to rewrite it with current info.
- **If it doesn't exist** — generate one from scratch.

### Step 2: Scan the repo

Silently gather information. Do not narrate what you're scanning.

**Read these files if they exist:**
- `package.json` — scripts, dependencies, project name
- `requirements.txt`, `pyproject.toml`, `setup.py`, `setup.cfg` — Python deps
- `go.mod` — Go project
- `Cargo.toml` — Rust project
- `composer.json` — PHP deps
- `Gemfile` — Ruby deps
- `pom.xml`, `build.gradle`, `build.gradle.kts` — Java/Kotlin deps
- `pubspec.yaml` — Flutter/Dart
- `Makefile` — build commands
- `.env.example` or `.env.local` — required environment variables
- `tsconfig.json` — TypeScript config
- `vercel.json`, `netlify.toml`, `Dockerfile` — deployment config
- `.eslintrc*`, `.prettierrc*`, `biome.json` — code style tools

**Check directory structure:**
- What's in the root? (`app/`, `src/`, `lib/`, `pages/`, `tests/`, `public/`, etc.)
- How deep is the project? (monorepo vs single package)
- Where do key files live?

**Detect patterns from code (quick scan, not exhaustive):**
- State management approach (Redux, Zustand, Context, Vuex, etc.)
- Data fetching patterns (TanStack Query, SWR, fetch, axios, etc.)
- Styling approach (Tailwind, CSS Modules, styled-components, etc.)
- Testing framework (Jest, Vitest, Playwright, pytest, etc.)
- Naming conventions visible in file/folder names

### Step 3: Generate the CLAUDE.md

Use this format. Skip any section that has nothing useful to say.

```markdown
# [Project Name]

[Stack one-liner — framework + language + database + deployment]

## Commands
- `[command]` — [what it does]
- `[command]` — [what it does]

## Key Paths
- `[path/]` — [what's in it]
- `[path/file]` — [what it does]

## Conventions
- [Pattern detected from the code]
- [Another pattern]

## Environment Variables
- `[VAR_NAME]` — [what it's for]
```

**Guidelines for each section:**

**Commands:** Pull from `package.json` scripts, `Makefile`, `Cargo.toml`, or whatever the project uses. Include dev, build, test, lint, and any project-specific commands. Skip obvious ones like `start` if it's just `node index.js`.

**Key Paths:** List the most important directories and files — the ones someone new would need to find first. Not every directory, just the ones that matter. Include brief descriptions.

**Conventions:** Only include patterns you actually detected in the code. "Uses Zustand with granular selectors" is good if you saw it. Don't guess or assume conventions.

**Environment Variables:** Pull from `.env.example` or `.env.local`. If neither exists but you can infer required vars from the code (e.g., `process.env.DATABASE_URL`), list those. Skip this section if no env vars are needed.

### Step 4: Present for review

Output the generated CLAUDE.md in chat. Do NOT write it to a file yet. Ask the user to review it and suggest changes.

### Step 5: Recommend plugins

Based on the detected stack, recommend which Claude Code plugins to install. Use this mapping:

| Stack Signal | Plugin | Why |
|-------------|--------|-----|
| `package.json` has `"next"` | fullstack-dev-skills | nextjs-developer, react-expert skills |
| `package.json` has `"react"` | fullstack-dev-skills | react-expert skill |
| `package.json` has `"vue"` | fullstack-dev-skills | vue-expert skill |
| `tsconfig.json` exists | fullstack-dev-skills | typescript-pro skill |
| Python project | fullstack-dev-skills | python-pro skill |
| Go project | fullstack-dev-skills | golang-pro skill |
| Rust project | fullstack-dev-skills | rust-engineer skill |
| Any project | superpowers | brainstorming, writing-plans, debugging, TDD, verification |
| Any project | commit-commands | commit, commit-push-pr |
| Any project | code-review | code review before merging |
| Tests exist | playwright (if browser tests) | playwright-expert skill |
| Supabase/Postgres | supabase | Supabase MCP tools |
| Vercel deployment | vercel | Vercel deployment/logs |
| Security-sensitive (auth, user data) | security-guidance | secure-code-guardian, security-reviewer |

Only recommend plugins that aren't already installed. Check the system reminder for currently available skills.

Format:
```
## Recommended Plugins
- **superpowers** — core workflow skills (brainstorming, planning, debugging, TDD)
- **fullstack-dev-skills** — nextjs-developer, react-expert, typescript-pro for your stack
- **commit-commands** — structured git commits
```

### Step 6: Save if approved

If the user approves the CLAUDE.md:
1. Write it to `CLAUDE.md` in the project root
2. If a CLAUDE.md already existed, confirm before overwriting

If the user wants changes, make them and present again.

## Key Principles

- **Minimal and practical** — write what a senior dev would write to onboard someone in 5 minutes
- **Detect, don't assume** — only document patterns you actually found in the code
- **Skip empty sections** — no "## Testing\nNo tests yet." Just don't include it.
- **Opinionated about format, not about the project** — the CLAUDE.md format is consistent, but the content reflects what's actually in the repo
