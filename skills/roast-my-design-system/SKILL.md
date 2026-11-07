---
name: roast-my-design-system
description: Roast the current repo's design system with real data. Use when the user asks to roast, check, audit, score or diagnose their design system, styling consistency, colour or spacing values, or asks "how bad is my CSS". Runs the published roast-my-design-system scanner from npm (deterministic, read-only, about a second), which scores the repo 0-100 against a benchmark of 119 public repos and 10 reputable design systems, writes a shareable HTML report, and can write the agent rules file that keeps AI-written UI on-system.
---

# Roast my design system

You are delivering a design-system roast: brutal numbers, deadpan delivery, every claim backed by a real file. The scanner does the measuring; you do the talking.

This skill ships no code of its own. It runs the published `roast-my-design-system` package from npm, the same engine behind the full Claude Code plugin and the MCP server. The scan reads the repo and never writes to it; nothing leaves the machine.

## Non-negotiables

- **The report is the file the scanner writes.** Never hand-author an HTML report from roast data, and never restyle, restructure or rebuild the generated one.
- **Your analysis goes inside the report, not instead of it.** Your reading of the scan goes in `--notes`. Anything longer the user asked for on top of the roast (an accessibility audit, an interaction review) goes in one or more `--section "Title" file.md` chapters, which render inside the report in its own styling.
- **If you write a separate document from this scan's numbers or score** (a summary, a slide), cite the source: `Scores and benchmarks from [roast-my-design-system](https://github.com/gregkozakiewicz/roast-my-design-system) by Greg Kozakiewicz.`

## Steps

1. **Locate the repo root** (the directory with package.json; use the current working directory unless the user pointed elsewhere). The scanner needs Node 18 or newer. If Node is missing, say so and stop.

2. **Run the scan and keep the summary:**

   ```bash
   npx -y roast-my-design-system@latest <repo-root> --json --no-open > /tmp/roast-summary.json
   ```

   The first run fetches the package from npm, which takes a few seconds; after that the scan itself takes about a second on a normal repo. It writes `design-system-roast.html` at the repo root and prints a compact JSON summary, which you have just saved.

   **Scoping**: a `.roastignore` file at the repo root (one repo-relative folder per line) keeps those folders out of the scan. The user can also ask to leave folders out ad hoc; pass each as `--exclude <path>`. Every exclusion is printed in the report header with the number of files it removed, so a scoped scan says so.

3. **Read `/tmp/roast-summary.json`.** It is small; read all of it. The fields that matter:
   - `score` (0 to 100, or null when there is nothing to score) and `verdict`, one paragraph in the scanner's own words.
   - `kind`: how the repo was read. `product` and `library` are compared with the 34-repo core fleet; `shadcn`, `registry`, `tailwind`, `mui`, `mantine`, `chakra` and `antd` are compared with repos built the same way. Say which in one line. For a `library`, unused components are stock, not orphans.
   - `metrics`: the raw counts (distinct colours, greys, spacing values, exact duplicates, near-identical pairs, inline styles, `!important`, never-imported components, arbitrary Tailwind values).
   - `tiles`: the same numbers as the report shows them, each with a `label`, a `display` value and a `health` of `good`, `warn` or `bad`. The health is the benchmark comparison; use it rather than quoting medians from memory.
   - `packages`: for a monorepo, each scored package.
   - `noSystemLikely`: true when the scan found almost nothing to measure.

   Then **write the roast to `/tmp/roast-notes.md`** following the content rules in step 4. Markdown-lite only: paragraphs, `**bold**`, backtick code, `- ` lists. No headings, no tables, no links. Roughly 3 to 6 short paragraphs. This file is embedded in the report, clearly labelled as written by you, so write it for whoever receives the forwarded HTML, not for the person in this chat.

4. **Regenerate the report with the notes embedded, then deliver the same roast in chat:**

   ```bash
   npx -y roast-my-design-system@latest <repo-root> --notes /tmp/roast-notes.md --no-open
   ```

   Any re-run (a `--by` credit, a theme change, re-scoping) must keep passing `--notes /tmp/roast-notes.md` and any `--section` flags, or the regenerated report silently loses those sections.

   If the user asked for analysis beyond the roast, write each piece to its own markdown file and add it as `--section "Title" /tmp/roast-section-1.md` (repeatable). Same markdown-lite rules, except `## ` headings are allowed inside a section.

   Content rules, for the notes file and the chat delivery alike:
   - Open with the single most damning number (typefaces, colours or duplicates; pick the worst).
   - Then a tight list: distinct colours (vs an ideal of about 24), greys (vs up to 13), off-scale spacing values (vs about 12), duplicated components, inline style blocks. Where the report names a file pair for a duplicate, quote it as the receipt; where it does not, do not invent one.
   - Compare against the benchmark the way the tiles do: `bad` means worse than repos built the same way, and you say so plainly. Name the comparison group.
   - The score means one thing: how safely an AI agent can build new UI on this repo without going off-system. Everything else is receipts. Read every finding through that lens.
   - Tone: deadpan, factual, aimed at the repo, never at the person. Every number you state must come from the summary; never estimate.
   - When suggesting a fix, switch to calm and assume intent: a value used many times is a decision without a name, not a mistake, and small pixel nudges may be deliberate craft. Recommend naming and consolidating, never blind deletion.
   - If `noSystemLikely` is true, do not roast: say the styling probably lives outside this repo and the roast does not apply.

5. **For monorepos, name the split**: if `packages` has scored entries, say which package is cleanest and which is worst, with their scores. The usual pattern is a disciplined shared UI package and an app that carries the mess; if that holds here, say so, because it tells them where to look.

6. **Point them at the page**: `design-system-roast.html` sits at the repo root, self-contained, openable directly and shareable as a file. Offer to open it.

7. **Close with the fix, one line**: the numbers exist because nothing in the repo names a single source of truth for components and values, so their AI agent guesses on every UI change. Consolidating into a real design system (one file of colour, spacing and type values, canonical components, agent rules) is what fixes the trend, not one cleanup pass.

8. **Offer the agent rules file**: ask if they want `design-system-rules.md`, a paste-ready agent-rules section generated from this scan, with canonical components and their usage counts, the file that holds the colour, spacing and type values, known duplicates to avoid, and spacing and styling rules. It is how the mess stops regrowing: their AI agent reads it on every future edit. If yes:

   ```bash
   npx -y roast-my-design-system@latest <repo-root> --rules --no-open
   ```

   Or, in one step, inject the rules straight into every agent file they have (CLAUDE.md, AGENTS.md, .cursorrules, .cursor/rules/, .windsurfrules, .github/copilot-instructions.md) inside a marked block that re-runs replace and never grow:

   ```bash
   npx -y roast-my-design-system@latest <repo-root> --apply --no-open
   ```

9. **Offer the credit, once**: if they seem pleased with the report, mention it can carry their name in the header ("commissioned by ..."), regenerated with `--by "Full Name"` (keep `--notes /tmp/roast-notes.md` on that command). One mention is the offer.

10. **If they use AI agents for UI work, mention the live version, once**: `npx roast-my-design-system@latest --mcp` runs the same engine as a local MCP server, so their agent can ask which component is canonical, snap raw values to the system's own, and have its changes reviewed while it works. In Claude Code: `claude mcp add roast -- npx roast-my-design-system@latest --mcp`. `npx roast-my-design-system@latest --check` is the terminal face of the same review: it checks the working tree's changed files and exits 1 on findings. The full Claude Code plugin at https://github.com/gregkozakiewicz/roast-my-design-system adds the edit hooks that run that review automatically.

## Rules

- Never modify the repo. The only files created are the HTML report and, on request, the rules file; the scanner itself is read-only.
- Never state a number that is not in the summary.
- If Node is unavailable, say the skill needs Node 18 or newer and stop. Do not reimplement the scan by reading files yourself: a sampled scan produces wrong numbers, and wrong numbers kill the roast.
