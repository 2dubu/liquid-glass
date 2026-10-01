# Liquid Glass for iOS

A Markdown skill for implementing, migrating, reviewing, and diagnosing iOS Liquid Glass in **SwiftUI and UIKit**. Claude Code and Codex share the same guidance and API references.

The scope is native iOS. Other Apple platforms and web reproductions are outside this edition.

## Install

### Claude Code

```text
/plugin marketplace add 2dubu/liquid-glass
/plugin install liquid-glass@liquid-glass
```

For a local checkout, load the plugin for one session:

```sh
claude --plugin-dir ./plugins/liquid-glass
```

### Codex

With a Codex CLI that supports plugins:

```sh
codex plugin marketplace add 2dubu/liquid-glass
codex plugin add liquid-glass@liquid-glass
```

For a local checkout, use the checkout path as the marketplace source instead of the GitHub repository. Start a new task/session after installation to pick up the skill.

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

## Contribute

Keep guidance focused on decisions that matter in an app. Link technical claims to Apple documentation, check snippets against the intended SDK and deployment target, and verify affected behavior in the target app. A successful compile does not establish rendering, accessibility, or device performance.

## License

[MIT](LICENSE)
