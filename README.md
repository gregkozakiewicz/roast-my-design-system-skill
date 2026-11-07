# roast-my-design-system, the skill edition

Your AI can write the UI. This makes sure it writes your UI.

This is the skill-only edition of [roast-my-design-system](https://github.com/gregkozakiewicz/roast-my-design-system), built for the plugin directories of Claude and OpenAI. It holds one skill and nothing that runs: no hooks, no local server, no scripts. When you ask for a roast, the skill runs the published scanner from npm, reads the result, and tells you what the numbers mean.

## What you get

- **A score from 0 to 100** for how safely an AI agent can build new UI on your repo without going off-system, measured against 119 public repos and 10 reputable design systems.
- **The receipts.** Every distinct colour and its near-identical twins, the greys, the off-scale spacing values, the duplicated and never-imported components, the inline styles, the `!important`s, the arbitrary Tailwind values. Each count names where it lives.
- **A shareable report.** One self-contained HTML file, light and dark, with the agent's reading of the scan embedded and labelled as such.
- **The agent rules file.** On request, a paste-ready section for CLAUDE.md, AGENTS.md or your Cursor rules, generated from the scan, so the mess stops regrowing.

The scan is deterministic and read-only. It needs Node 18 or newer. Nothing leaves your machine.

## Install

**Claude Code**

```
/plugin marketplace add gregkozakiewicz/roast-my-design-system-skill
/plugin install roast-my-design-system@roast-my-design-system-skill
```

**Codex**

```
codex plugin marketplace add gregkozakiewicz/roast-my-design-system-skill
codex plugin add roast-my-design-system@roast-my-design-system-skill
```

Either way, the first thing to say is "roast my design system".

## Which edition do I want?

| | Skill edition (this repo) | Full plugin |
|---|---|---|
| The roast, the report, the rules file | Yes | Yes |
| Edit hooks that review every UI change as the agent makes it | No | Yes |
| MCP tools your agent calls while it writes UI | No | Yes |
| Ships code | No, it runs the npm package | Yes, the engine itself |
| Where it lives | Plugin directories | [GitHub](https://github.com/gregkozakiewicz/roast-my-design-system) and [npm](https://www.npmjs.com/package/roast-my-design-system) |

Both run the same engine, so the score is the same. The full plugin is the one to install from GitHub when you want the guard as well as the roast.

## Links

- Website: [www.roastds.com](https://www.roastds.com)
- The engine, the benchmark and the full plugin: [github.com/gregkozakiewicz/roast-my-design-system](https://github.com/gregkozakiewicz/roast-my-design-system)
- Questions and bugs: [the issue tracker](https://github.com/gregkozakiewicz/roast-my-design-system/issues)

MIT licence. Made by [Greg Kozakiewicz](https://gregkozakiewicz.com).
