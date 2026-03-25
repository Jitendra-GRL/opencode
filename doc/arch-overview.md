Optimizing tool selection...

Read [](file:///c%3A/GRL/Projects/opencode/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/STATS.md)

Search complete: Find architecture docs

Searched for files matching `**/README*.md`, 39 matches

Searched for text `architecture`, 35 results

Read [](file:///c%3A/GRL/Projects/opencode/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/packages/web/src/content/docs/server.mdx#1-1), lines 1 to 200

Searched for text `architecture` (`**/*.md`), 5 results

Read [](file:///c%3A/GRL/Projects/opencode/packages/opencode/src/acp/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/packages/opencode/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/specs/project.md)

Searched for text `Terminal` (`**/packages/**/*.md`), 28 results

Read [](file:///c%3A/GRL/Projects/opencode/packages/app/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/packages/web/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/AGENTS.md)

Read [](file:///c%3A/GRL/Projects/opencode/packages/desktop/README.md)

Read [](file:///c%3A/GRL/Projects/opencode/packages/desktop-electron/README.md)

**Building Blocks**

- Core runtime & agents: The CLI package (opencode) hosts the Bun-based runtime that talks to models, manages tool execution, and exposes the built-in `build` (full access) and `plan` (read-only) personas, as documented in README.md. This is the process your terminal points at, regardless of which UI you’re driving.
- Terminal-first UI shell: The Solid/Vite front end under README.md renders the TUI/console experience (same codebase powers the browser view embedded in the desktop builds). It speaks to the core runtime over its local HTTP/WebSocket APIs to show sessions, terminals, and file diffs.
- Native desktop shells: The Tauri-based wrapper in README.md (and the Electron flavor in README.md) bundle the TUI plus a local runtime so you can install OpenCode as a desktop app with system tray, auto-updates, and OS integrations.
- Agent Client Protocol (ACP) service: The clean-room ACP implementation described in README.md splits responsibilities across `agent.ts`, `client.ts`, `session.ts`, and `server.ts`. It lets external IDEs (e.g. Zed) talk to OpenCode over JSON-RPC/stdio while reusing the same session model and tool stack.
- Project/session service: The HTTP surface outlined in project.md defines how a single OpenCode daemon can juggle multiple projects, worktrees, sessions, prompts, and file mutations. Higher-level clients (TUI, desktop, or ACP) call these endpoints to enumerate work, spawn sessions, revert changes, etc.

**Use Cases**

- Local-first coding copilot: Because the runtime is open source, model-agnostic, and runs next to your repo, you get Claude/OpenAI/local LLM assistance without uploading code elsewhere, with the focused terminal UI emphasized in README.md.
- Safe codebase exploration: Switching to the `plan` agent gives you a read-only assistant (it refuses edits and shell commands) which is ideal for audits, pair-planning, or onboarding to new repos, per README.md.
- Remote control / multi-device workflows: The documented client/server split lets you keep the runtime on a beefy machine while driving it from another client (mobile, thin browser, or ACP IDE) so the TUI is just one of many possible front ends, as highlighted in README.md.
- IDE integration via ACP: Editors that already speak the Agent Client Protocol can delegate their “AI actions” to OpenCode’s ACP server, gaining file IO, tool execution, and session persistence without re-implementing the agent, per README.md.
- Multi-project automation hubs: Teams can host a single always-on OpenCode daemon, create per-project sessions, share or revert changes, and even expose logs via the endpoints in project.md, turning OpenCode into an internal AI build/review service instead of just a personal CLI.

Natural next steps if you want to dive deeper would be: (1) spin up `opencode acp` and connect a compatible IDE to see the protocol in action, or (2) run the Tauri desktop shell to explore how the UI maps onto the session API.
