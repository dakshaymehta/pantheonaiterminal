<h1 align="center">PantheonAI Terminal</h1>
<p align="center">Open-Source AI Agent with Search, Financial Data, and More</p>
<p align="center"><strong>A Project by <a href="https://www.thepantheonai.com" target="_blank">PantheonAI</a></strong></p>
<p align="center">Follow us on <a href="https://www.x.com/financecopilot" target="_blank">Twitter/X</a>!</p>

<p align="center"><code>npm i -g @pantheonai/terminal</code> (Coming Soon!)</p>

![Codex demo GIF using: codex "explain this codebase to me"](./.github/demo.gif)

---

<details>
<summary><strong>Table&nbsp;of&nbsp;Contents</strong></summary>

- [Experimental Technology Disclaimer](#experimental-technology-disclaimer)
- [Quickstart](#quickstart)
- [Why PantheonAI Terminal?](#why-pantheonai-terminal)
- [Core Features (Planned)](#core-features-planned)
- [Security Model & Permissions](#securitymodelpermissions)
  - [Platform sandboxing details](#platform-sandboxing-details)
- [System Requirements](#systemrequirements)
- [CLI Reference](#clireference)
- [Memory & Project Docs](#memoryprojectdocs)
- [Non‑interactive / CI mode](#noninteractivecimode)
- [Recipes](#recipes)
- [Installation](#installation)
- [Configuration](#configuration)
- [FAQ](#faq)
- [Contributing](#contributing)
  - [Development workflow](#development-workflow)
  - [Writing high‑impact code changes](#writing-highimpact-code-changes)
  - [Opening a pull request](#opening-a-pull-request)
  - [Review process](#review-process)
  - [Community values](#community-values)
  - [Getting help](#getting-help)
  - [Contributor License Agreement (CLA)](#contributor-license-agreement-cla)
    - [Quick fixes](#quick-fixes)
  - [Releasing `pantheonai-terminal`](#releasing-pantheonai-terminal)
- [Security & Responsible AI](#securityresponsibleai)
- [License](#license)

</details>

---

## Experimental Technology Disclaimer

PantheonAI Terminal is an experimental project under active development, evolving from the OpenAI Codex CLI. It is not yet stable, may contain bugs, incomplete features, or undergo breaking changes. We're building it in the open with the community and welcome:

- Bug reports
- Feature requests
- Pull requests
- Good vibes

Help us improve by filing issues or submitting PRs (see the section below for how to contribute)!

## Quickstart

Install globally (package name TBD, placeholder below):

```shell
npm install -g @pantheonai/terminal # Placeholder name - not published yet!
```

Next, set your OpenAI API key as an environment variable:

```shell
export OPENAI_API_KEY="your-api-key-here"
```

> **Note:** This command sets the key only for your current terminal session. To make it permanent, add the `export` line to your shell's configuration file (e.g., `~/.zshrc`).

Run interactively:

```shell
pantheonai # Placeholder command name
```

Or, run with a prompt as input (and optionally in `Full Auto` mode):

```shell
pantheonai "Summarize the latest tech news"
```

```shell
pantheonai --approval-mode full-auto "Analyze the sentiment of recent AAPL news headlines"
```

That's it – PantheonAI will understand your request, potentially search the web, interact with files or APIs, and show you the result. Approve any actions like file changes or commands as needed.

---

## Why PantheonAI Terminal?

PantheonAI Terminal, an open-source project by <a href="https://www.thepantheonai.com" target="_blank">PantheonAI</a>, is built for developers, analysts, and anyone who **lives in the terminal**. It aims to provide ChatGPT‑level reasoning **plus** the power to:

-   **Access Real-time Information:** Integrate live internet search results.
-   **Analyze Data:** Perform financial data analysis, news aggregation, and more (inspired by Bloomberg Terminal).
-   **Interact with Specialized Systems:** Connect to specific protocols like MCP servers (details TBD).
-   **Automate Tasks:** Run code, manipulate files, and iterate on tasks – all under version control.

In short, it's an _AI-driven terminal_ designed to be your intelligent command center, combining powerful AI capabilities with the transparency and community collaboration of open source.

-   **Zero setup** — Bring your OpenAI API key and it just works! (Other models/APIs potentially supported in the future).
-   **Configurable Autonomy:** Control how much the agent can do automatically while maintaining safety via sandboxing.
-   **Multimodal** — Pass in screenshots or diagrams to implement features ✨ (inherited capability).
-   **Fully Open-Source:** We believe in the power of community. You can inspect the code, contribute features, report issues, and help shape the future of PantheonAI Terminal. Find us on GitHub! (Link to repo will be here).

---

## Core Features (Planned)

PantheonAI Terminal aims to integrate several key capabilities:

1.  **Conversational AI Agent:** Leverage large language models for understanding prompts and executing tasks.
2.  **Internet Search:** Fetch and synthesize information from the web.
3.  **MCP Server Integration:** Interact with designated MCP servers (*details to be specified*).
4.  **Open Source Bloomberg Functionality:** Provide access to financial data, news, and analysis tools using open APIs and data sources.
5.  **Extensibility:** A modular design for adding new tools and data sources.

*(See `instructions.md` for more details on the vision and roadmap)*

---

## Security Model & Permissions

PantheonAI lets you decide _how much autonomy_ the agent receives and auto-approval policy via the `--approval-mode` flag (or the interactive onboarding prompt):

| Mode                      | What the agent may do without asking            | Still requires approval                                         |
| ------------------------- | ----------------------------------------------- | --------------------------------------------------------------- |
| **Suggest** <br>(default) | • Read any file in the repo                     | • **All** file writes/patches <br>• **All** shell/Bash commands |
| **Auto Edit**             | • Read **and** apply‑patch writes to files      | • **All** shell/Bash commands                                   |
| **Full Auto**             | • Read/write files <br>• Execute shell commands | –                                                               |

In **Full Auto** every command is run **network‑disabled** and confined to the
current working directory (plus temporary files) for defense‑in‑depth. PantheonAI
will also show a warning/confirmation if you start in **auto‑edit** or
**full‑auto** while the directory is _not_ tracked by Git, so you always have a
safety net.

Coming soon: you'll be able to whitelist specific commands to auto‑execute with
the network enabled, once we're confident in additional safeguards.

### Platform sandboxing details

The hardening mechanism PantheonAI uses depends on your OS:

- **macOS 12+** – commands are wrapped with **Apple Seatbelt** (`sandbox-exec`).

  - Everything is placed in a read‑only jail except for a small set of
    writable roots (`$PWD`, `$TMPDIR`, `~/.pantheonai`, etc.).
  - Outbound network is _fully blocked_ by default – even if a child process
    tries to `curl` somewhere it will fail.

- **Linux** – we recommend using Docker for sandboxing, where PantheonAI launches itself inside a **minimal
  container image** and mounts your repo _read/write_ at the same path. A
  custom `iptables`/`ipset` firewall script denies all egress except the
  OpenAI API. This gives you deterministic, reproducible runs without needing
  root on the host. You can read more in [`run_in_container.sh`](./codex-cli/scripts/run_in_container.sh)

Both approaches are _transparent_ to everyday usage – you still run `pantheonai` from your repo root and approve/reject steps as usual.

---

## System Requirements

| Requirement                 | Details                                                         |
| --------------------------- | --------------------------------------------------------------- |
| Operating systems           | macOS 12+, Ubuntu 20.04+/Debian 10+, or Windows 11 **via WSL2** |
| Node.js                     | **22 or newer** (LTS recommended)                               |
| Git (optional, recommended) | 2.23+ for built‑in PR helpers                                   |
| RAM                         | 4‑GB minimum (8‑GB recommended)                                 |

> Never run `sudo npm install -g`; fix npm permissions instead.

---

## CLI Reference

| Command                              | Purpose                             | Example                              |
| ------------------------------------ | ----------------------------------- | ------------------------------------ |
| `pantheonai`                         | Interactive REPL                    | `pantheonai`                         |
| `pantheonai "…"`                     | Initial prompt for interactive REPL | `pantheonai "fetch GOOG stock price"` |
| `pantheonai -q "…"`                  | Non‑interactive "quiet mode"        | `pantheonai -q --json "summarize HN"` |
| `pantheonai completion <bash\|zsh\|fish>` | Print shell completion script       | `pantheonai completion zsh`          |

Key flags: `--model/-m`, `--approval-mode/-a`, and `--quiet/-q`.

---

## Memory & Project Docs

PantheonAI merges Markdown instructions in this order:

1. `~/.pantheonai/instructions.md` – personal global guidance
2. `pantheonai.md` at repo root – shared project notes
3. `pantheonai.md` in cwd – sub‑package specifics

Disable with `--no-project-doc` or `PANTHEONAI_DISABLE_PROJECT_DOC=1`.

---

## Non‑interactive / CI mode

Run PantheonAI head‑less in pipelines. Example GitHub Action step:

```yaml
- name: Update market summary via PantheonAI
  run: |
    npm install -g @pantheonai/terminal
    export OPENAI_API_KEY="${{ secrets.OPENAI_KEY }}"
    pantheonai -a auto-edit --quiet "generate today's market summary in summary.md"
```

Set `PANTHEONAI_QUIET_MODE=1` to silence interactive UI noise.

---

## Recipes

Below are a few bite‑size examples you can copy‑paste. Replace the text in quotes with your own task. *(Examples will evolve as features are added)*

| ✨  | What you type                                                                   | What happens                                                                                              |
| --- | ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| 1   | `pantheonai "Refactor the login component to use async/await"`                  | PantheonAI rewrites the code, runs tests (if configured), and shows the diff.                             |
| 2   | `pantheonai "What were the main points of the last Fed meeting?"`               | Searches the web for reliable sources, summarizes the key takeaways.                                      |
| 3   | `pantheonai "Get the current price and P/E ratio for TSLA"`                     | Queries a financial data API and returns the requested information.                                       |
| 4   | `pantheonai "Write unit tests for utils/data_parser.py"`                        | Generates tests, executes them, and iterates until they pass (similar to Codex).                          |
| 5   | `pantheonai "Explain this complex regex: ..."`                                  | Outputs a step‑by‑step human explanation.                                                                 |
| 6   | `pantheonai "Carefully review this repo, and propose 3 high impact PRs"`        | Suggests impactful PRs in the current codebase.                                                           |
| 7   | `pantheonai "Connect to MCP server example.com and retrieve status"`            | (Planned Feature) Executes the necessary commands/API calls for MCP interaction.                          |
| 8   | `pantheonai "Plot the YTD performance of the S&P 500"`                          | (Planned Feature) Generates a chart or provides data suitable for plotting.                               |

---

## Installation

<details open>
<summary><strong>From npm (Coming Soon)</strong></summary>

```bash
# Placeholder - package not yet published
npm install -g @pantheonai/terminal
# or
yarn global add @pantheonai/terminal
```

</details>

<details>
<summary><strong>Build from source</strong></summary>

```bash
# Clone the repository and navigate to the CLI package
git clone https://github.com/path/to/pantheonai-terminal.git # TODO: Update repo URL
cd pantheonai-terminal/cli-package-dir # TODO: Update path

# Install dependencies and build
npm install
npm run build

# Get the usage and the options
node ./dist/cli.js --help # TODO: Verify entry point

# Run the locally‑built CLI directly
node ./dist/cli.js # TODO: Verify entry point

# Or link the command globally for convenience
npm link # This will likely link the command based on package.json name
```

</details>

---

## Configuration

PantheonAI looks for config files in **`~/.pantheonai/`**.

```yaml
# ~/.pantheonai/config.yaml
model: o4-mini # Default model, gpt-4o recommended for complex tasks
fullAutoErrorMode: ask-user # or ignore-and-continue
# Add future config options here, e.g., API keys for financial data
# financialDataSource: alphavantage # Example
# webSearchEngine: duckduckgo # Example
```

You can also define custom instructions:

```markdown
# ~/.pantheonai/instructions.md
- Always provide sources for financial data or news summaries.
- Prefer using Python for data analysis scripts unless otherwise specified.
- When generating code, adhere to PEP 8 guidelines.
```

---

## FAQ

<details>
<summary>OpenAI released a model called Codex in 2021 - is this related?</summary>

In 2021, OpenAI released Codex, an AI system designed to generate code from natural language prompts. That original Codex model was deprecated as of March 2023 and is separate from the CLI tool.

</details>

<details>
<summary>How do I stop PantheonAI from touching my repo?</summary>

PantheonAI inherits the safety model from Codex CLI and always runs potentially risky operations (like file modifications or shell commands) in a **sandbox first**. If a proposed action looks suspicious, you can simply answer **n** when prompted, and nothing will happen to your working tree. The level of autonomy can be configured using the `--approval-mode` flag.

</details>

<details>
<summary>Does it work on Windows?</summary>

Not directly. It requires [Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/install) – PantheonAI has been tested on macOS and Linux with Node ≥ 22.

</details>

<details>
<summary>Which models are supported?</summary>

Currently targets models available via the OpenAI API ([Responses API](https://platform.openai.com/docs/api-reference/responses)). The default is `o4-mini`, but pass `--model gpt-4o` or set `model: gpt-4o` in your config file to override. Support for other models or local LLMs could be added in the future.

</details>

---

## Contributing

This project is under active development and the code will likely change pretty significantly. We'll update this message once that's complete!

More broadly we welcome contributions – whether you are opening your very first pull request or you're a seasoned maintainer. At the same time we care about reliability and long‑term maintainability, so the bar for merging code is intentionally **high**. The guidelines below spell out what "high-quality" means in practice and should make the whole process transparent and friendly.

### Development workflow

- Create a _topic branch_ from `main` – e.g. `feat/interactive-prompt`.
- Keep your changes focused. Multiple unrelated fixes should be opened as separate PRs.
- Use `npm run test:watch` during development for super‑fast feedback.
- We use **Vitest** for unit tests, **ESLint** + **Prettier** for style, and **TypeScript** for type‑checking.
- Before pushing, run the full test/type/lint suite:

  ```bash
  npm test && npm run lint && npm run typecheck
  ```

- If you have **not** yet signed the Contributor License Agreement (CLA), add a PR comment containing the exact text

  ```text
  I have read the CLA Document and I hereby sign the CLA
  ```

  The CLA‑Assistant bot will turn the PR status green once all authors have signed.

```bash
# Watch mode (tests rerun on change)
npm run test:watch

# Type‑check without emitting files
npm run typecheck

# Automatically fix lint + prettier issues
npm run lint:fix
npm run format:fix
```

### Writing high‑impact code changes

1. **Start with an issue.** Open a new one or comment on an existing discussion so we can agree on the solution before code is written.
2. **Add or update tests.** Every new feature or bug‑fix should come with test coverage that fails before your change and passes afterwards. 100 % coverage is not required, but aim for meaningful assertions.
3. **Document behaviour.** If your change affects user‑facing behaviour, update the README, inline help (`pantheonai --help`), or relevant example projects.
4. **Keep commits atomic.** Each commit should compile and the tests should pass. This makes reviews and potential rollbacks easier.

### Opening a pull request

- Fill in the PR template (or include similar information) – **What? Why? How?**
- Run **all** checks locally (`npm test && npm run lint && npm run typecheck`). CI failures that could have been caught locally slow down the process.
- Make sure your branch is up‑to‑date with `main` and that you have resolved merge conflicts.
- Mark the PR as **Ready for review** only when you believe it is in a merge‑able state.

### Review process

1. One maintainer will be assigned as a primary reviewer.
2. We may ask for changes – please do not take this personally. We value the work, we just also value consistency and long‑term maintainability.
3. When there is consensus that the PR meets the bar, a maintainer will squash‑and‑merge.

### Community values

- **Be kind and inclusive.** Treat others with respect; we follow the [Contributor Covenant](https://www.contributor-covenant.org/).
- **Assume good intent.** Written communication is hard – err on the side of generosity.
- **Teach & learn.** If you spot something confusing, open an issue or PR with improvements.

### Getting help

If you run into problems setting up the project, would like feedback on an idea, or just want to say _hi_ – please open a Discussion or jump into the relevant issue. We are happy to help.

Together we can make PantheonAI Terminal an incredible tool. **Happy hacking!** :rocket:

### Contributor License Agreement (CLA)

All contributors **must** accept the CLA. The process is lightweight:

1. Open your pull request.
2. Paste the following comment (or reply `recheck` if you've signed before):

   ```text
   I have read the CLA Document and I hereby sign the CLA
   ```

3. The CLA‑Assistant bot records your signature in the repo and marks the status check as passed.

No special Git commands, email attachments, or commit footers required.

#### Quick fixes

| Scenario          | Command                                                                                   |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Amend last commit | `git commit --amend -s --no-edit && git push -f`                                          |
| GitHub UI only    | Edit the commit message in the PR → add<br>`Signed-off-by: Your Name <email@example.com>` |

The **DCO check** blocks merges until every commit in the PR carries the footer (with squash this is just the one).

### Releasing `pantheonai-terminal`

To publish a new version of the CLI, run the release scripts defined in `package.json` (details TBD).

---

## Security & Responsible AI

Have you discovered a vulnerability or have concerns about model output? Please e‑mail **security@openai.com** (or specify a dedicated project email if preferred). We will respond promptly.

---

## License

This repository is licensed under the [Apache-2.0 License](LICENSE).
