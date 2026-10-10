# PdfCraft aniefork roadmap

This is the fork's own record: [arcanii/pdfcraft](https://github.com/arcanii/pdfcraft), a fork of [storytold/pdfcraft](https://github.com/storytold/pdfcraft).

Upstream's [ROADMAP.md](ROADMAP.md) is kept **identical to upstream** here. Both sides used to add log lines at the top of that file, so it conflicted on every merge. Work done in the fork is logged in this file instead.

- **Fork work:** add a line to the log below. Do not edit `ROADMAP.md`.
- **A branch meant as an upstream pull request:** follow upstream's rules there (`CLAUDE.md`), including its `ROADMAP.md` log line. Cut the branch from `upstream/main`, not from the fork's `main`.

## Sync status

| | |
|---|---|
| Last merge of upstream | 2026-10-10, upstream `0bc619c` (PdfCraft 0.5.0), merge commit `e4b2f7b` |
| Fork is behind upstream by | 0 commits at that merge |
| Fork carries | 3 changes upstream lacks (below) |

## What the fork carries

Changes in the fork that upstream does not have. Update the last column when one is offered or merged; delete the row once a merge of upstream brings it back.

| Change | Fork commits | Upstream |
|---|---|---|
| Workspace `rust-version` is 1.95, the real minimum (egui 0.36 needs it), and every crate declares it. Upstream says 1.90, so an older toolchain fails with a dependency error instead of a clear minimum. | `5e65ddd`, `2969b0b` | Not offered yet |
| An XFA script's time limit runs from when the script engine is ready. It counted the engine's start-up, so on a busy machine a trivial script was abandoned and the form's scripts were turned off (this also made two `ui-egui` `xfa.rs` tests flaky). | `11e15f6` | Not offered yet |
| The app says when a form's scripts were turned off (a toast, then the notice bar with a JavaScript Console button; `xfa_scripts_off` in `doc_info`), and the notice bar's text wraps beside its buttons. | `03b2789`, plus seven translations in `e4b2f7b` | Not offered yet |

## Syncing with upstream

```sh
git fetch upstream
git merge upstream/main
```

Sync often: upstream lands dozens of commits a day, and small merges are easy. With `ROADMAP.md` out of the way, these still conflict when both sides touch them:

- **`ATTRIBUTION.toml`:** each translation catalog's SHA-256 is recorded there. When both sides changed a catalog, take upstream's entry, then recompute the hash of the merged file (`cargo xtask assets` lists the stale ones).
- **`parity/acrobat-features.toml`:** when both sides edited the same feature. Take upstream's text and re-apply the fork's addition.
- **New catalog coverage tests:** upstream adds interface languages with a test that every `tl!("…")` string is translated. A string the fork added must then be translated in the new catalog (`cargo test -p pdfcraft-ui-egui --lib i18n` names what is missing).

After a merge, run the gates in `CLAUDE.md` before committing it, and update the table above.

## Sending changes upstream

Upstream has no `CONTRIBUTING.md`, pull request template or CLA; its rules are `AGENTS.md` and `CLAUDE.md`. What its merged pull requests look like (88 of the last 100 came from forks, as of 2026-10-10):

- One topic per pull request, from a branch of the fork. Upstream squashes it into one commit and adds the number, so the title is the commit title: `M12: what changed`, or `Issue #123: what changed`.
- The description says what was wrong, what changed, and which checks were run locally (`cargo fmt --check`, `cargo clippy -- -D warnings`, tests) and which were left to CI.
- CI runs the tests on Linux and Windows, the wasm32 check and the dependency licence check. A first-time contributor's CI waits for a maintainer to approve it.
- Duplicates are closed in favour of whichever landed first, so look for an existing issue or pull request before opening one.
- Credit: everyone is credited by GitHub username. A `[people.<username>]` entry in `contributors/people.toml` adds a real or display name; only the person themselves adds it, in their own pull request.

## Log

Newest first.

- **2026-10-10 (merge of upstream 0.5.0):** Merged 180 upstream commits (`0bc619c`). The code merged cleanly; `ROADMAP.md`, `ATTRIBUTION.toml` and `parity/acrobat-features.toml` conflicted and were resolved by hand. Upstream had added catalog coverage tests for German, Italian, Brazilian Portuguese, Ukrainian, Bulgarian, Hungarian and Arabic, so the fork's "scripts are turned off" notice was translated in those catalogs too. 1.95 is still the real minimum Rust version. This file was started, and `ROADMAP.md` went back to upstream's text.
- **2026-10-10 (M12, XFA scripts turned off):** When a runaway script turns an XFA form's scripts off, the app now says so: a toast as it happens (at open or after a click), and the notice bar from then on, with a JavaScript Console button (the error was only in the console, so calculations and buttons just stopped). The engine reports the state (`Document::xfa_scripts_off`, and `xfa_scripts_off` in `doc_info` for agents) instead of the UI reading error text. The notice bar's text now wraps beside its buttons instead of running under them (the French sentence did). The sentence is translated in 13 catalogs; Telugu and Czech show it in English.
- **2026-10-09 (M12, XFA script time limits):** An XFA script's time limit (1 s for scripts that run on open) counted the engine's start-up too: a new thread and context, plus the engine's own set-up for the first script in a process (≈ 60–170 ms when idle). On a busy machine a trivial initialize script could pass the limit, be abandoned and turn the form's scripts off (the cause of the flaky `xfa.rs` UI tests: they failed 8 of 8 runs under full CPU load). The limit now runs from when the engine is ready; start-up has its own 10 s cap, and a trivial warm-up script takes the first-in-process set-up. Under the same load: 24 of 24 runs pass. Runaway scripts are still abandoned as before.
- **2026-10-09 (M0, minimum Rust version):** The workspace `rust-version` said 1.90, but egui 0.36 needs 1.95 and rten 0.26 needs 1.94, so older toolchains failed with a dependency error. It now says 1.95, checked by building every target with 1.95.0 (1.94.0 is refused); the README's Get started names it. Every crate inherits it (11 of 34 did, so the app, CLI and core crates declared no minimum).
