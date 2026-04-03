# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

- Build: `npm run build` (builds all packages)
- Clean: `npm run clean`
- Check (Lint/Typecheck): `npm run check` (runs `biome check` and `tsgo`)
- Test all: `npm run test` (all packages)
- Run specific test: `npx tsx ../../node_modules/vitest/dist/cli.js --run <path>` (from package root)
- Run non-LLM tests: `./test.sh` (fast, no API keys needed)
- Run from source: `./pi-test.sh` (can be run from any directory)
- OSS Weekend (Close/Open): `node scripts/oss-weekend.mjs --mode=[close|open] [--end-date=YYYY-MM-DD] [--git]`
- Release (Lockstep): `npm run release:patch` or `npm run release:minor`

## Architecture

This is a monorepo for an agentic framework designed for high performance and modularity. It uses a unified event-based system (`AgentEvent`) that allows the same agent logic to drive different UIs (TUI, Web, Slack).

### Core Packages
- **packages/ai**: LLM provider abstraction (types, standardized events). Decouples agent logic from specific providers.
- **packages/agent**: Core agent loop, message types, and tool execution engine.
- **packages/coding-agent**: Main developer-focused CLI tool. Includes interactive mode, command handling, and "skills" (dynamic CLI tools).
- **packages/tui**: Event-driven Terminal UI components for a rich terminal experience.
- **packages/mom**: Self-managing Slack bot that can autonomously build and use its own tools.
- **packages/pods**: Management of remote GPU pods running vLLM for agentic workloads.
- **packages/web-ui**: Web interface for the agent.

### High-Level Flow
1. **Providers (ai)**: Map raw LLM responses (Claude, GPT, Qwen, etc.) to a standardized stream of events (`text`, `tool_call`, `thinking`, `usage`, `stop`).
2. **Agent (agent)**: Receives events, manages conversation state, and executes tools using a reasoning loop.
3. **Interface (coding-agent/tui/mom/web-ui)**: Presents the agent's output and processes user input, often using an event-based rendering system.

## Development Rules

### Code Quality
- **No `any` types**: Use proper type definitions or check `node_modules` for external API types.
- **No inline imports**: Always use standard top-level imports. Avoid `await import(...)` and type imports in type positions.
- **Configurable Keybindings**: Never hardcode keys (e.g., `ctrl+x`). Add defaults to `DEFAULT_EDITOR_KEYBINDINGS` or `DEFAULT_APP_KEYBINDINGS`.
- **Path Resolution**: Use `src/config.ts` for package assets. Never use `__dirname` directly.

### Testing
- Run tests from the package root, not the repo root.
- If you create or modify a test file, you MUST run it and iterate until it passes.
- For `packages/coding-agent/test/suite/`, use `test/suite/harness.ts` and faux providers. No real API keys/paid tokens.
- Issue-specific regressions: `packages/coding-agent/test/suite/regressions/<issue-number>-<short-slug>.test.ts`.

### Git & Collaboration
- **Lockstep versioning**: All packages share the same version number.
- **Selective Commits**: ONLY commit files YOU changed. NEVER use `git add -A` or `git add .`.
- **Issue Tracking**: Use `gh` CLI. Always read all comments on an issue.
- **Labels**: Add `pkg:*` labels (e.g., `pkg:agent`, `pkg:ai`) to issues and PRs.
- **Changelog**: Each package has its own `CHANGELOG.md`. New entries MUST go under `## [Unreleased]`.
- **Commits**: Include `fixes #<number>` or `closes #<number>` in the commit message.
- **No Fluff**: Keep answers technical, concise, and direct. No emojis in code, commits, or PR comments.
