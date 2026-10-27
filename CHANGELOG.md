# Changelog

## 0.3.0

- Renamed from claude-bingo to llm-bingo (display name "LLM Bingo"). Claude
  Code now reserves plugin names beginning with `claude-`, so
  `claude plugin validate --strict` rejects the old name. Everything moves:
  the PyPI distribution (`llm-bingo`), the Python package (`llm_bingo`), the
  console script, the launcher (`scripts/llm-bingo`), the plugin and both
  in-repo catalogs, and the repository (`lavallee/llm-bingo`; GitHub redirects
  the old URL). The phrase bank and scoring are unchanged.
- To reinstall the plugin, add the marketplace again and install
  `llm-bingo@llm-bingo` (or `llm-bingo@lyra-forge`); remove the old
  `claude-bingo` plugin. A pip/pipx/uv tool install moves with
  `pipx install llm-bingo`; the old PyPI project stays at 0.2.1.
- The `claude-bingo` console script is kept as a deprecated alias of
  `llm-bingo` and will be removed in a later release.
- Saved boards and personal phrase banks now live under `llm-bingo/` in the
  XDG data and config directories. Files left under `claude-bingo/` are still
  read until a new one exists, so nothing is lost on upgrade.

## 0.2.1

- `score` no longer counts claude-bingo's own output. A rendered board
  self-matches 15 of its 24 squares, so every round you played inflated the
  next one. Blocks carrying a board border are skipped.

## 0.2.0

- Codex support: ships `.codex-plugin/plugin.json` and a Codex catalog at
  `.agents/plugins/marketplace.json`, so `codex plugin marketplace add
  lavallee/claude-bingo` works the same way the Claude Code one does.
  `claude-bingo` is also listed in both catalogs of `lyra-forge/marketplace`.
- One launcher for every path: `scripts/claude-bingo` at the plugin root,
  replacing the per-harness `PYTHONPATH` incantations in the skill.
- A readable error instead of `ModuleNotFoundError: tomllib` on Python older
  than 3.11, naming a newer interpreter if one is on PATH.
- Board rows are sized to their tallest cell, so a fresh board no longer
  renders with two blank lines in every row.
- Long labels keep their count when the cell has to truncate them.
- Tests cover the packaging surface that a checkout can't: version lockstep
  across four manifests, HTTPS-only plugin sources, and the skill's launcher
  path.

## 0.1.0

- First release: `board`, `score`, `phrases`, a 65-phrase bank, and a Claude
  Code plugin.
