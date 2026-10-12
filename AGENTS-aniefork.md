# aniefork: this checkout is a fork

This repository is arcanii/pdfcraft, a fork of storytold/pdfcraft (the `upstream` remote).
[`CLAUDE.md`](CLAUDE.md) and [`AGENTS.md`](AGENTS.md) are upstream's files and stay unchanged here;
everything in them still applies (never crash, no Adobe assets, clean room, quality gates).
This file says where the fork differs. Read it and [`ROADMAP-aniefork.md`](ROADMAP-aniefork.md) at
the start of a session. The sister forks arcanii/wordcraft and arcanii/gridcraft follow the same
rules.

- **Log fork work in `ROADMAP-aniefork.md`, not in upstream's documents.** `ROADMAP.md`, `docs/`,
  `CLAUDE.md` and `AGENTS.md` stay identical to upstream so that merging upstream doesn't conflict.
  Where `CLAUDE.md` says to update `ROADMAP.md` at the end of a session, add a line to the log in
  `ROADMAP-aniefork.md` instead, and keep its "What the fork carries" table true.
- **Fork-private files carry `aniefork` in their path**, so upstream can never add the same one:
  - a document sits beside the upstream document it stands in for, with a `-aniefork` suffix:
    `ROADMAP.md` → `ROADMAP-aniefork.md`, `AGENTS.md` → `AGENTS-aniefork.md`;
  - code goes in an `aniefork` module of the crate it belongs to
    (`crates/ui-egui/src/aniefork/`), with its tests.
- **Upstream files get hooks only:** a `mod` line, or a short call into the fork's module, with
  `(aniefork)` in the comment above it. Keep the logic on the fork's side of the call. Every hook is
  listed in `ROADMAP-aniefork.md`, so a merge conflict is settled by taking upstream's code and
  putting the hook back.
- **State the UI needs between frames** lives in the fork's module (egui's temporary data, keyed by
  an `aniefork.*` id) rather than in new fields on upstream's structs.
- **Interface text the fork adds** is translated in the fork's own table, into every interface
  language, never in upstream's catalogs (`i18n/*.tsv` must all hold the same entries, their hashes
  are recorded in `ATTRIBUTION.toml`, and upstream changes them constantly). Translate from the
  meaning, with the words upstream's catalogs already use; never from Adobe's or another product's
  localisation.
- **The exception: fixes on their way upstream.** A fix to upstream's own code that is meant as a
  pull request is written in upstream's style and edited in place. While the fork carries one, it
  is listed file by file in `ROADMAP-aniefork.md`. A feature that is meant to stay in the fork
  follows the rules above instead.
- **A branch meant as an upstream pull request** follows upstream's rules, its progress documents
  included. Cut it from `upstream/main`, not from the fork's `main`, in a separate worktree: never
  switch this checkout to it. `ROADMAP-aniefork.md` has the steps.
- **Syncing with upstream:** `ROADMAP-aniefork.md` lists the steps and what can still conflict.
