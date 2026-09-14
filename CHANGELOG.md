# Changelog

## 1.0.0

Five agents instead of three, and four config paths that were being read from the
wrong place.

**Cursor and Pi join the inventory.** Cursor's own bundle names nine skill
directories, four of which belong to other agents — `~/.claude/skills`,
`~/.claude/plugins`, `~/.codex/skills` and `~/.agents/skills` — so installing it
does not add a column of its own findings, it lights up rows that were already on
the list. Pi is the opposite and reads nobody else's, keeping its own under
`~/.pi/agent/skills`. Each invocation is written from the identity that agent
actually reads, because copying the wrong one is silent: Pi takes
`/skill:<declared name>`, Cursor a bare `/<declared name>`. Both marks are the
products' real ones.

**OpenCode's paths are resolved rather than assumed.** They were hardcoded to
`~/.config/opencode`, and four things move them: `XDG_CONFIG_HOME` **moves** the
config root and the skills root with it, `OPENCODE_CONFIG_DIR` **adds** a second,
`OPENCODE_CONFIG` and `OPENCODE_CONFIG_CONTENT` each merge a further
configuration in, and `XDG_CACHE_HOME` and `XDG_DATA_HOME` move the fetched-skills
root and the MCP token file. Under any of them the always-on token figure on the
bar was computed from the wrong disk.

**`opencode.jsonc` is read as OpenCode reads it** — comments and all, and it wins
over an `opencode.json` beside it, which is now reported as shadowed rather than
quietly used instead. Strict JSON is still parsed first and its answer stands, so
a plain config is never put through the comment stripper at all.

**The row was rebuilt.** `kind`, `scope` and `agents` come off the right-hand
chain and follow a fixed name column, so they start at the same place on every
row; a long name wraps to two lines and then elides, and the row grows only when
it does. The actions chip rides in the name column's own slack. The agent band
spans the full width above the counts, and the grouping switch moved up beside
them — five agents needed the width the switch had been taking.

**`all` no longer disappears when you need it.** Categories belong to skills
alone, so filtering to servers emptied the shelf row and took the only control
that clears a filter with it. It now stays for as long as there is something to
undo.

**The QML gate stopped measuring the wrong thing.** Quickshell exposes its config
root as the module `qs`, which `qmllint` does not know, so every Omarchy type read
as missing: 388 unqualified-access warnings that do not exist, and 209
missing-property findings hidden behind them. With the module resolved and
`pragma ComponentBehavior: Bound` in place, `Panel.qml` reports 220 warnings
against 831, none of them unqualified access, and CI holds a per-file ceiling.

## 0.2.0

Categories can now be ordered, not just filled. The category index grew a
**Sort** switch — biggest first, smallest first, or your own order — and the
shelves themselves drag into place: grab a chip, drop it, and the group headers
in the main list follow. Any drag switches to your own order on its own; a
**Moved X to position N** line with **Undo** confirms the drop for ten seconds,
then falls back to the standing hint. The order lives in the widget's own
`categories.json` beside everything else it keeps, and nothing an agent loads
is touched by any of it.

## 0.1.0

First release. Every skill, plugin and MCP server Claude Code, OpenCode and Codex load, in one
searchable list on the bar, with what each one costs in tokens on every turn and the command that
invokes it in the spelling that particular agent expects.

**Read-only over everything that is not its own.** No `SKILL.md` is opened for writing, no agent's
configuration file is touched, nothing is deleted or moved, and the helper starts no other program.
It writes two files, both under `~/.config/agent-skills`: `categories.json` for the categories you
filed things under, and `descriptions.json` for the notes you wrote yourself, which no agent reads.
The test suite asserts that rather than the README promising it.

### What it does

- **One list across three agents**, deduplicated by where the file really is rather than by name or
  by path, so a skill symlinked into three roots is one row carrying three agent marks instead of
  three rows repeating themselves — and two copies that have stopped sharing their contents are told
  apart by content hash.
- **The always-on token figure**, computed the way Claude Code's own extensions browser computes it,
  per row and per agent. Three agents read one disk and are charged three different bills for it, so
  the bar prints the bill of the agent that is actually running, read out of `/proc` without starting
  anything.
- **The invocation on your clipboard**, addressed through its plugin where a skill arrives inside
  one, and opened as a grid of choices where the skill documents arguments. Nothing is claimed until
  it happens: the panel writes, reads back, and says **Copied** only when the read agrees.
- **Categories you own.** Fourteen are guessed from the description and a skill the rules cannot
  place lands in Unsorted rather than in whichever category was the residual. Every guess is one
  keystroke from being corrected, and the correction goes to the widget's own file, never to a
  skill's. An expanded row says how it was filed, and that line has a `×`: it is worth reading
  while you are still deciding whether to trust the shelving and settled news afterwards, so it
  switches off on every card at once and comes back from the category index.
- **Flags that mean something** — a skill answering to two names across agents, an expired MCP
  token, a copy that has drifted from its original. A category the classifier was unsure of is not a
  problem and is not reported as one.
- **A note of your own**, with `^D`, kept in the widget's own file and drawn above the description in
  that skill's card. No agent reads it and it is in no figure anywhere, so it costs nothing on any
  turn — write what a skill is for in your own words, or in your own language, and the search reads it
  too. The description under it is the file's own and is reported as it stands: what an agent loads on
  its next turn is not a bar widget's to change.
### Requirements

Omarchy 4 with its Quickshell bar and `python3` at `/usr/bin/python3`. No Python package beyond the
standard library: the frontmatter reader is hand-written rather than reaching for PyYAML, which is not
in the Omarchy base set and which rejects real skill files all three agents read without complaint.
