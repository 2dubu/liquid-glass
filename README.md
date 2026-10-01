<img width="100%" alt="image" src="https://github.com/user-attachments/assets/583ee6ce-47c7-4976-a478-55101dc73a0a" />

# Liquid Glass for iOS

A Markdown skill for implementing, migrating, reviewing, and diagnosing iOS Liquid Glass in **SwiftUI and UIKit**. Claude Code and Codex share the same guidance and API references.

The scope is native iOS. Other Apple platforms and web reproductions are outside this edition.

## Install

### Claude Code

Run in your terminal:

```sh
claude plugin marketplace add 2dubu/liquid-glass
claude plugin install liquid-glass@liquid-glass
```

Inside Claude Code, the corresponding `/plugin marketplace add` and `/plugin install` commands are also available. See [Claude's installation guide](https://code.claude.com/docs/en/discover-plugins).

### Codex

Use a Codex CLI with `codex plugin add` support. Command syntax was checked with CLI 0.146.0:

```sh
codex plugin marketplace add 2dubu/liquid-glass
codex plugin add liquid-glass@liquid-glass
```

The repository includes a native Codex marketplace catalog. See [OpenAI's packaging and marketplace guide](https://developers.openai.com/plugins/build/plugins) for desktop and managed setups.

After installation or an update, start a new session so the host loads the installed skill. Restart the desktop app if its plugin catalog has not refreshed.

### Update

For Claude Code:

```sh
claude plugin marketplace update liquid-glass
claude plugin update liquid-glass@liquid-glass
```

For Codex, refresh the marketplace snapshot, then install from the refreshed catalog:

```sh
codex plugin marketplace upgrade liquid-glass
codex plugin add liquid-glass@liquid-glass
```

### Local development

From a checkout's root, load the Claude plugin for one session:

```sh
claude --plugin-dir ./plugins/liquid-glass
```

For a local Codex marketplace, use the checkout's absolute **repository root** instead of `2dubu/liquid-glass` in the install command. The plugin directory `plugins/liquid-glass` is not the marketplace root. Use either the GitHub source or the local checkout for the `liquid-glass` marketplace name.

## Use

- "Review this SwiftUI sheet and toolbar migration; keep iOS 18 support."
- "Add an expandable glass action group with Reduce Motion support."
- "These UIKit glass controls overlap instead of merging. Diagnose the hierarchy."
- "Explain why the navigation bar looks different inside a hosting controller."

Invoke `/liquid-glass:liquid-glass` in Claude Code or `$liquid-glass:liquid-glass` in Codex when installed as a plugin. Normal automatic skill selection remains enabled.

## Contents

[SKILL.md](plugins/liquid-glass/skills/liquid-glass/SKILL.md) selects the references relevant to the request:

- SwiftUI custom effects and system components
- SwiftUI search/tab examples, stable identity, state updates, and performance diagnosis
- UIKit custom effects and system components
- Design, accessibility, and performance decisions
- Availability and older-iOS compatibility
- Diagnosis of appearance, interaction, and version-specific behavior

The skill folder contains only Markdown, with short code snippets inside the relevant guides. Plugin manifests live outside that folder and provide packaging for Claude Code and Codex.

Apple Docs MCP and an iOS framework disassembler can support investigations when available. The plugin does not bundle an MCP server or require either tool. Guidance links to primary sources and distinguishes documented behavior from implementation observations and inference.

## Repository structure

| Path | Purpose |
|---|---|
| `AGENTS.md` | Shared repository maintenance instructions |
| `.claude-plugin/marketplace.json` | Claude Code marketplace catalog |
| `.agents/plugins/marketplace.json` | Codex marketplace catalog and installation policy |
| `plugins/liquid-glass/plugin.json` | Portable Agent Plugins manifest with OpenAI presentation metadata |
| `plugins/liquid-glass/.claude-plugin/plugin.json` | Claude Code plugin manifest |
| `plugins/liquid-glass/.codex-plugin/plugin.json` | Compatibility manifest for Codex clients that do not read the portable metadata |
| `plugins/liquid-glass/skills/liquid-glass/` | The single shared skill and its Markdown references |

Both catalogs resolve the same plugin directory. Host-specific manifests preserve compatibility without separate copies of the skill.

## Contribute

Read [AGENTS.md](AGENTS.md) before changing the repository. [Codex](https://learn.chatgpt.com/docs/agent-configuration/agents-md) and [current Claude Code](https://code.claude.com/docs/en/memory#agentsmd) can read it directly; no `CLAUDE.md` bridge is included. Claude Code requires v2.1.277 or later for direct loading and, by default, prefers a `CLAUDE.md` or `CLAUDE.local.md` in the working directory or its ancestors when one exists. The linked guide explains the Project instructions setting. These are contributor instructions; the installed plugin's entry point is `SKILL.md`.

Keep guidance focused on decisions that matter in an app. Link technical claims to Apple documentation, check snippets against the intended SDK and deployment target, and verify affected behavior in the target app. A successful compile does not establish rendering, accessibility, or device performance.

Validate packaging changes from the repository root:

```sh
claude plugin validate . --strict
claude plugin validate ./plugins/liquid-glass --strict
git diff --check
```

Keep shared metadata and versions aligned across the three plugin manifests. Bump the plugin version when changing shipped plugin files so cached installs can receive the update.

## License

[MIT](LICENSE)
