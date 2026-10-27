# llm-bingo

A 5×5 bingo board of LLM verbal tics, scored against your own Claude Code
transcripts.

Formerly `claude-bingo` — renamed in 0.3.0 because Claude Code reserves plugin
names that start with `claude-`. See the [changelog](CHANGELOG.md) for moving an
existing install.

```
         B               I               N               G               O
  ┌───────────────┬───────────────┬───────────────┬───────────────┬───────────────┐
  │               │               │               │ I'll go ahead │               │
  │    Happy to   │    leverage   │    footgun    │      and      │    nuanced    │
  │       ×5      │      ×53      │       ×1      │       —       │      ×16      │
  ├───────────────┼───────────────┼───────────────┼───────────────┼───────────────┤
  │               │     Great     │               │               │               │
  │  load-bearing │   question!   │  escape hatch │    sit with   │   idiomatic   │
  │      ×413     │       ×2      │      ×15      │       —       │       ×6      │
  ├───────────────┼───────────────┼───────────────┼───────────────┼───────────────┤
  │   Let me be   │               │               │  Let me know  │               │
  │     direct    │   crucially   │      FREE     │       if      │  blast radius │
  │       —       │      ×78      │     SPACE     │       —       │       ×7      │
  ├───────────────┼───────────────┼───────────────┼───────────────┼───────────────┤
  │               │               │               │   Would you   │     You're    │
  │   happy path  │  surface area │   meaningful  │   like me to  │   absolutely  │
  │       —       │       ×1      │      ×136     │       —       │    correct    │
  ├───────────────┼───────────────┼───────────────┼───────────────┼───────────────┤
  │               │               │    The key    │ single source │               │
  │  To be clear  │ comprehensive │    insight    │    of truth   │     robust    │
  │       ×3      │      ×23      │      ×10      │       ×1      │      ×23      │
  └───────────────┴───────────────┴───────────────┴───────────────┴───────────────┘

  BINGO! row 5, col I, col N
  18/25 squares · 793 utterances · 3,027 transcripts

  ── worst offenders ────────────────────────────────────────
    413  load-bearing
         …nothing I touched there was load-bearing.
    136  meaningful
         …no meaningful difference between the two paths.
     78  crucially
         …and, crucially, it never reaches the network.
```

Everything runs locally. It reads `~/.claude/projects/**/*.jsonl` and prints to
your terminal. There are no network calls and no dependencies beyond the Python
standard library.

## Install

Pick whichever of these you'll actually remember. All three end up running the
same code.

### Try it without installing anything

```bash
uvx llm-bingo board
uvx llm-bingo score
```

### As an agent plugin

Then just ask your agent to play bingo. Two marketplaces carry it — use either.

**From [Lyra Forge](https://github.com/lyra-forge/marketplace)**, alongside its
sibling plugins:

```text
# Claude Code
/plugin marketplace add lyra-forge/marketplace
/plugin install llm-bingo@lyra-forge

# Codex
codex plugin marketplace add lyra-forge/marketplace
codex plugin add llm-bingo@lyra-forge
```

**Or straight from this repository**, which ships its own one-entry catalog:

```text
# Claude Code
/plugin marketplace add lavallee/llm-bingo
/plugin install llm-bingo@llm-bingo

# Codex
codex plugin marketplace add lavallee/llm-bingo
codex plugin add llm-bingo@llm-bingo
```

Start a new session afterwards. Then ask for a bingo board in plain English, or
invoke the skill directly — `/llm-bingo:bingo` in Claude Code,
`$llm-bingo:bingo` in Codex.

The plugin needs no install step of its own: it runs the bundled package from
the plugin directory using whatever `python3` you already have. The Codex plugin
scores your *Claude Code* transcripts on the same machine, which is the joke.

### As a CLI you keep around

```bash
pipx install llm-bingo   # or: uv tool install llm-bingo
```

### From source

```bash
git clone https://github.com/lavallee/llm-bingo
cd llm-bingo
./scripts/llm-bingo board
```

Every path needs Python 3.11+ (for `tomllib`) and nothing else. Stock macOS
ships 3.9 — if that's what you have, `uvx llm-bingo` sidesteps it entirely.

## Use

```bash
llm-bingo board              # generate a fresh board
llm-bingo board --size 3     # smaller, for a quick round
llm-bingo board --seed 12345 # reproduce a specific board
llm-bingo score              # score it against the last 7 days
llm-bingo score --days 30    # ...or however far back you dare look
llm-bingo score --no-quotes  # skip the receipts
llm-bingo score --top 5      # a shorter walk of shame
llm-bingo phrases            # list the phrase bank
```

The board is saved to `$XDG_DATA_HOME/llm-bingo/board.json` (default
`~/.local/share/llm-bingo/board.json`), so `score` picks up whatever you last
generated. A board saved under the old `claude-bingo/` directory is still read
until you generate a new one; the same goes for a personal phrase bank.

A given `--seed` reproduces a given board only for a given phrase bank — add
phrases and the same seed deals a different hand.

## The phrase bank

65 phrases across 9 categories, in
[`llm_bingo/phrases.toml`](llm_bingo/phrases.toml). Each entry is a board
label and the regex that hunts for it:

```toml
[linkedin-brain]
"delve" = "\\bdelv(?:e|es|ing)\\b"
"tapestry" = "\\btapestry\\b"
```

Patterns are matched case-insensitively and in multiline mode, so `^` anchors to
the start of any line.

Add your own without touching the package in
`~/.config/llm-bingo/phrases.toml` — same format. A label that collides with
a bundled one overrides its pattern.

## Contributing

PRs to the phrase bank are the point of this repo. Add to an existing category
or start a new one. Two things make a good entry:

- **The label reads at a glance.** It lands in a 13-column cell.
- **The regex is specific enough to mean something.** `\bjust\b` will match
  everything and tell you nothing; `you'?re absolutely right` earns its square.

Run `llm-bingo phrases` to check yours loaded, and `llm-bingo score` to
see whether it actually catches anything.

## Layout

The repository is one Python package plus the manifests each harness looks for:

```
llm_bingo/                       the package — CLI, phrase loader, phrases.toml
scripts/llm-bingo                launcher the plugin runs; no install needed
skills/bingo/SKILL.md            the skill both harnesses load
.claude-plugin/plugin.json       Claude Code plugin manifest
.claude-plugin/marketplace.json  this repo as a one-entry Claude Code catalog
.codex-plugin/plugin.json        Codex plugin manifest
.agents/plugins/marketplace.json this repo as a one-entry Codex catalog
```

## Releasing

The version lives in four places — `pyproject.toml`,
`llm_bingo/__init__.py`, and both `plugin.json` files. `tests/` fails if they
drift, so bump them together, then:

```bash
python3 -m unittest discover tests
claude plugin validate . --strict
```

Tag and publish a GitHub release; CI builds the wheel and pushes it to PyPI via
trusted publishing. Plugin installs resolve from the default branch, so they
pick the change up as soon as it's pushed — no marketplace edit is needed unless
the entry itself changes.

## License

MIT
