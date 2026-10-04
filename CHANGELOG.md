# Changelog

## 1.1.1

Nothing you type, and no name a skill author chose, goes into a command line any
more, and a handful of inputs that could stop the scan or skew a figure no longer do.

**The writes take their change on stdin.** A note, a shelf label and the skill a
change is about used to travel as arguments to the helper, and an argument is in
`/proc/<pid>/cmdline` for every account on the machine to read while the process
lives. The panel now runs `category assign|unassign|create|style --stdin` and
`describe note --stdin` and writes the change as one JSON object: at most 16 KiB,
exactly the keys the verb takes, within five seconds, or nothing is written, and the
refusal never repeats what was sent. A skill named `-h` and a label of `--wip` are now
just strings: the label used to be refused and a note of `--` was dropped while the
panel reported it saved.

**The clipboard fallback is `wl-copy` reading stdin.** The copy that stays behind to
serve the clipboard kept the text in its arguments until something else was copied,
and a long update prompt was over what one argument may hold.

**Only the scan gets OpenCode's environment.** The panel now forwards every variable
the helper reads to find what OpenCode loads (`XDG_CONFIG_HOME`, `XDG_CACHE_HOME`,
`XDG_DATA_HOME`, `OPENCODE_CONFIG_DIR`, `OPENCODE_CONFIG`, `OPENCODE_CONFIG_CONTENT`,
`OPENCODE_DISABLE_EXTERNAL_SKILLS`); through 1.1.0 only the last one was passed, so
the panel counted directories OpenCode had stopped reading. The writes get `PATH`,
`HOME` and `PYTHONIOENCODING` only, and an empty `HOME` is left out rather than read
by Python as `/`.

**The update prompt quotes what it read.** Names, paths, versions, hashes and plugin
sources are in double quotes with JSON escaping, under a line saying they are data,
and the file path goes through the same cleaning as everything else: a directory
name with a newline in it could write a `source:` line of its choosing.

**One odd value no longer ends the scan.** A directory name that is not UTF-8, an
`expiresAt` or `lastUsedAt` that is a string or `1e400`, a `skillUsage` entry that is
not an object, and a list where the plugin catalog has a name each stopped the whole
scan with nothing printed. Each is now read past, and a skill that cannot be read is
one finding.

**Category changes refuse an unreadable store** instead of writing a fresh one over
it, as notes already did, and neither store can be written larger than it can be
read back.

**MCP command lines hide more.** `--github-token ghp_…`, `--client-secret …`,
`--x-api-key …`, `--bearer-token …`, `--cookie …` and the like, given as two
arguments, now hide their value as `--token …` always did.

Smaller: a skill filed on a shelf that does not exist goes back to the classifier
instead of leaving the list; `constructor` is no longer a shelf name, because every
map in the panel already has a key by that name; `~/.config/agent-skills` is made `0700` if it was not; a write that was
killed leaves no temp file for good; `~/.claude.json` and the plugin catalog cache may
be up to 16 MiB; findings, paths and plugin fields are bounded and stripped of control
characters, which `doctor` printed to the terminal as they were; the file manager
opens the path on disk rather than its display copy; a change that lands during a
scan gets a scan of its own; the panel waits past the helper's own deadline so a slow
disk shows a partial list rather than none; the scan wrapper no longer prints
`[1]+ Done` into `qs log`; and the remains of the removed `SKILL.md` writer are gone.

## 1.1.0

The panel cannot tell you a skill is out of date, and now it does not pretend to:
it hands the question to something that can answer it.

**Ask about one skill, or the whole list, in one shape.** A per-row **update** chip
(`^A` from the keyboard, for *ask* — `^U` belongs to the shell and clears the search
field) copies an agent-directed prompt built from everything the panel read about
that skill: its file, the declared version, the content hash, which agents load it,
and the install commit on the one kind of row that records one. **Updates**, beside
**Edit**, asks the same question about everything currently listed. *Currently
listed* is the operative phrase: every filter narrows the question exactly as the
counts narrow, built-ins stay out while they are hidden, and the button stands down
while the category editor is open or when nothing is listed. One builder serves both,
so a row never has a longer shape of its own.

**The instructions carry the weight.** Identifying the source is a step that is
allowed to fail (*do not guess at a repository*); only the rules the listed skills
can actually run into are included; verdicts are fixed (moved / unchanged / could not
identify) and the prompt ends on a checklist; local edits must be named before
anything is written, and the answering run writes nothing. A bulk prompt states its
own length and calls a missing number a failed report, so an agent cannot quietly skip
the middle of a long list. The clipboard confirmation names the prompt instead of
echoing thousands of characters.

**`origin` is no longer blank where it can be known.** A skill shipped inside a Claude
Code plugin now carries the commit that plugin was installed at, from
`installed_plugins.json`. Every other row reports none, and none is drawn as none.

Nothing here adds a read or a write: the same ten skill roots and configuration
files, the same two files under `~/.config/agent-skills/`, no network and no
subprocess in the helper.

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
