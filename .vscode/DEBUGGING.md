# Debugging pi-mono with VSCode

This project is a monorepo for an agentic framework. This guide helps you set up and use the consolidated debugging configurations in VSCode.

## Prerequisites

- **VSCode**: Latest version recommended.
- **Node.js**: >= 20.6.0 (required for `--env-file` support).
- **tsx**: The configurations use `npx tsx` to run TypeScript code directly without a build step.
- **Environment Variables**: Create a `.env` file in the root directory with your API keys. The launch configurations automatically load this file.

### Proxy / Custom Base URL
If you are using a proxy (like `localhost:8317`), you can override the base URL for different providers in your `.env` file:
```env
OPENAI_BASE_URL_PI=https://localhost:8317/v1
ANTHROPIC_BASE_URL_pi=https://your-anthropic-proxy.com/v1
GOOGLE_BASE_URL_pi=https://your-google-proxy.com/v1
```
These variables will override the hardcoded defaults in the models configuration.

## Debug Configurations

The following configurations are available in the VSCode Debug panel:

### CLI Entry Points
- **Debug: CLI (source)**: Runs the main coding agent CLI (`packages/coding-agent/src/cli.ts`) with the `help` command.
- **Debug: CLI (interactive)**: Runs the CLI in interactive mode (`--interactive`).
- **Debug: CLI (TUI)**: Runs the CLI in Terminal UI mode (`--tui`).
- **Debug: Mom (Slack Bot)**: Runs the Slack bot (`packages/mom/src/main.ts`).
- **Debug: AI CLI (source)**: Runs the AI abstraction CLI (`packages/ai/src/cli.ts`).
- **Debug: Pods CLI (source)**: Runs the Pods management CLI (`packages/pods/src/cli.ts`).

### Testing
- **Debug: Vitest (Current File)**: Runs the currently active test file with `vitest`. **Note:** Uses `PI_OFFLINE=1` by default to avoid large model downloads in `pods`.
- **Debug: Vitest (coding-agent)**: Runs all tests in the `coding-agent` package.
- **Debug: Vitest (agent)**: Runs all tests in the `agent` package.
- **Debug: Vitest (ai)**: Runs all tests in the `ai` package.

### Web UI
- **Debug: Web UI (Chrome)**: Debugs the web interface in Chrome. Requires the Vite server to be running.
  1. Start the Vite server: `npm run dev --workspace=packages/web-ui/example` or use the `npm: dev (web-ui)` task.
  2. Select **Debug: Web UI (Chrome)** from the Debug panel.

## Key Entry Points

- **packages/coding-agent/src/cli.ts**: The main entry point for the `pi` CLI.
- **packages/coding-agent/src/main.ts**: The main logic for handling commands and starting agent sessions.
- **packages/agent/src/agent-loop.ts**: The core reasoning loop of the agent.
- **packages/ai/src/providers/*.ts**: Individual LLM provider implementations.

## Suggested Breakpoints

- **`packages/coding-agent/src/main.ts`**: Set a breakpoint in `main()` to see how arguments are parsed.
- **`packages/agent/src/agent-loop.ts`**: Set a breakpoint in the main loop to observe how the agent processes events and tool calls.
- **`packages/ai/src/providers/openai-responses.ts`**: If you're debugging OpenAI or proxy issues, set a breakpoint in `createClient`.

## Common Tasks

You can run these from the VSCode Command Palette (`Ctrl+Shift+P`) -> `Tasks: Run Task`:
- **npm: build**: Build all packages.
- **npm: dev**: Start all packages in watch mode.
- **npm: check**: Run linting and typechecking.
- **npm: test**: Run all tests.
- **pi-test**: Run coding-agent tests directly from source.

## Troubleshooting

- **Node Version**: If you get errors about `--env-file`, ensure you are using Node.js >= 20.6.0.
- **Large Downloads**: If tests start downloading massive files, ensure `PI_OFFLINE=1` is set in your environment.
- **401/Unauthorized**: Check your `.env` file and ensure `OPENAI_BASE_URL_PI` is correct if you use a proxy.
