# Repository guidance

This repository distributes one Liquid Glass skill for native iOS, shared by Claude Code and Codex. These instructions govern maintenance of this repository; the installed skill starts at `plugins/liquid-glass/skills/liquid-glass/SKILL.md`.

## Content boundaries

- Cover SwiftUI and UIKit. Keep other Apple platforms and web implementations out of scope.
- Keep `skills/liquid-glass/` Markdown-only. Short Swift examples belong inside the relevant reference. Keep test apps, scripts, binary dumps, screenshots, and audit reports outside the repository.
- Keep `SKILL.md` concise: task selection and links to focused references. Add detailed API guidance to the appropriate reference instead of copying it into several files.
- Keep all resources needed by an installed skill inside the plugin directory, with relative links. Repository instructions and contributor documentation are not installed skill dependencies.
- Apple Docs MCP and the disassembler are optional investigation tools. Do not add bundled MCP servers, hooks, or executable components without an explicit scope change.

## Technical accuracy

- Verify version-sensitive claims against current Apple documentation and the intended SDK. Distinguish Xcode/SDK version, deployment target, and running iOS version/build.
- Keep public contracts, compiler checks, implementation observations, and runtime results separate. A successful compile does not prove rendering, accessibility, or performance.
- Use public APIs in application examples. Do not turn private implementation details into production recommendations.
- When binary inspection is needed, record the OS/build and binary UUID in the external investigation notes. Move only actionable, appropriately qualified guidance into the skill.
- Link claims to their primary sources. Avoid blanket claims that every documented behavior has been runtime-tested or remains current indefinitely.

## Packaging

- Preserve the plugin and skill names `liquid-glass` and the marketplace install ID `liquid-glass@liquid-glass`.
- Both marketplace catalogs must point to `./plugins/liquid-glass`: `.claude-plugin/marketplace.json` for Claude Code and `.agents/plugins/marketplace.json` for Codex.
- `plugins/liquid-glass/plugin.json` is the portable manifest. Keep its shared metadata consistent with `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json` inside that plugin directory.
- Keep the portable manifest's `extensions.com.openai.interface` equal to the Codex compatibility manifest's `interface`. Older Codex clients still need the compatibility manifest.
- Bump the version in all three plugin manifests when shipping plugin changes. Keep plugin versions out of marketplace entries so they cannot disagree with the manifests.
- Do not add empty component directories or duplicate the skill for each host. Preserve the README banner and keep install/update instructions aligned with the package.
- Keep repository-root and plugin-root `LICENSE` and `NOTICE` copies identical so installed plugins retain the license and attribution. Keep the license identifier aligned across all three plugin manifests.
- Before changing host integration, consult the current [OpenAI packaging guide](https://developers.openai.com/plugins/build/plugins), [Claude manifest reference](https://code.claude.com/docs/en/plugins-reference), and [Claude marketplace reference](https://code.claude.com/docs/en/plugins/marketplace-reference).

## Validation and publication

Run these from the repository root after packaging changes:

```sh
claude plugin validate . --strict
claude plugin validate ./plugins/liquid-glass --strict
git diff --check
```

Also parse every JSON manifest, check metadata consistency and local Markdown links, and confirm the skill directory contains only Markdown. When available, use Codex's read-only `plugin/read` API to verify both marketplace catalogs resolve the expected plugin and skill; report the client version and any unverified loading behavior.

For Swift example changes, typecheck against the intended SDK/deployment target and verify affected behavior in an external app as needed. Do not add test scaffolding to this documentation plugin.

Write repository documents, commit messages, PR titles/descriptions, and review comments in English. Follow the existing Conventional Commits style, such as `docs(readme): ...` or `fix(plugin): ...`. Keep planning documents and investigation artifacts out of commits unless explicitly requested.
