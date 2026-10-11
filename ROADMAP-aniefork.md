# PdfCraft aniefork roadmap

This is the fork's own record: [arcanii/pdfcraft](https://github.com/arcanii/pdfcraft), a fork of [storytold/pdfcraft](https://github.com/storytold/pdfcraft).

Upstream's [ROADMAP.md](ROADMAP.md) is kept **identical to upstream** here. Both sides used to add log lines at the top of that file, so it conflicted on every merge. Work done in the fork is logged in this file instead.

- **Fork work:** add a line to the log below. Do not edit `ROADMAP.md`.
- **A branch meant as an upstream pull request:** follow upstream's rules there (`CLAUDE.md`), including its `ROADMAP.md` log line. Cut the branch from `upstream/main`, not from the fork's `main`.

The fork's rules are in [AGENTS-aniefork.md](AGENTS-aniefork.md); the sister forks arcanii/wordcraft and arcanii/gridcraft keep the same two files.

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

## Changes in upstream files

Each change the fork carries is a fix to upstream's own code, edited in place in upstream's style, because it is meant as a pull request (the exception in `AGENTS-aniefork.md`). After merging upstream, check these are still there. When upstream takes a change, its rows go.

| Change | Upstream files it touches |
|---|---|
| Minimum Rust version | `Cargo.toml` (`rust-version` and the comment above it); `rust-version.workspace = true` in 23 crate manifests; one line in `README.md` ▸ Get started |
| XFA script time limit | `crates/js/src/xfa.rs` (`run_within`, `run_within_limits`, `warm_up`, `run_here`), `crates/js/src/formcalc.rs` (`run_formcalc_within`), `crates/js/src/tests.rs`, `crates/ui-egui/tests/xfa.rs` |
| Scripts-off notice | `crates/engine/src/lib.rs` (`xfa_scripts_off`), `crates/automation/src/lib.rs` (`doc_info`), `crates/ui-egui/src/js_ui.rs` (`handle_js`, `scripts_off_notice`), `crates/ui-egui/src/canvas.rs` (`DocView::scripts_off_noted`, `notices`), their tests, one sentence in 13 catalogs under `crates/ui-egui/src/i18n/`, those catalogs' hashes in `ATTRIBUTION.toml`, and `form.xfa-dynamic` in `parity/acrobat-features.toml` |

The last row is where the fork departs from its own rules: a fork-private feature would keep its text in a table of its own and leave the catalogs, `ATTRIBUTION.toml` and `parity/` alone. Those three are what conflicts when upstream is merged.

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

Only when the owner asks for it, one topic per pull request. How the sister forks do it (storytold/gridcraft#265 and #266, storytold/wordcraft#570 and #571), which pdfcraft follows:

- **A branch cut from `upstream/main`, in a separate worktree** (never by switching this checkout): `git worktree add -b <branch> <local dir> upstream/main`. A worktree on a local disk whose repository is on the network share needs `GIT_CONFIG_COUNT=1 GIT_CONFIG_KEY_0=safe.directory GIT_CONFIG_VALUE_0=<worktree>` in the environment of every git command (nothing is written to the global configuration). Branch names are short and plain: `xfa-script-time-limit`.
- **Upstream's style, no `aniefork` anywhere,** and upstream's documents updated as its `CLAUDE.md` and `AGENTS.md` ask: a line at the top of the `ROADMAP.md` log, `parity/acrobat-features.toml` when a feature changes, interface text through `tl!` with an entry in every catalog that has a coverage test, and the catalogs' hashes in `ATTRIBUTION.toml`.
- **Contributor names:** `[people.arcanii]` in `contributors/people.toml`, as its own commit ("Add arcanii to the contributor names"), the same entry as in the sister forks. It goes in the first pull request; a branch cut before that lands does not have it.
- **The pull request:** the title is the commit title upstream will keep when it squashes (`M12: what changed`, upstream's task-id rule; the sister projects use `Area: what changed`). The body has What was wrong, What changed and Checks (for a feature: What it does, How, Not in this pull request, Checks), says honestly which checks ran locally and on which upstream commit, and names what was left to CI. Pushed to `origin` and opened with `gh pr create --repo storytold/pdfcraft --base main --head arcanii:<branch>`.
- **Before opening:** search upstream for a duplicate (it closes the later one), and refresh the branch. Upstream adds a line at the top of `ROADMAP.md` with every commit, so a branch conflicts there within hours.
- **After upstream merges it:** it squashes, so the fork's own commits differ from upstream's. Merge upstream, take its version where they overlap, and take the change out of the tables above.

What upstream's merged pull requests look like, as of 2026-10-10: 88 of the last 100 came from forks; one maintainer merges, usually within hours, sometimes pushing a small follow-up; CI runs the tests on Linux and Windows, the wasm32 check and the dependency licence check, and a first-time contributor's CI waits for approval. There is no `CONTRIBUTING.md`, pull request template or CLA.

## Building

- **Rust:** 1.95 or newer (egui 0.36). The fork declares it; upstream still says 1.90. `cargo xtask ci` also needs the `wasm32-unknown-unknown` target.
- **The UI tests** render through wgpu. On a machine with an NVIDIA card and an integrated GPU, run them with `WGPU_BACKEND=dx12`: egui_kittest then picks the software adapter.
- **This checkout is on a network share:** builds are slow, and a transient "can't find crate" (E0463) only needs a rerun. A worktree and its `CARGO_TARGET_DIR` on a local disk are much faster.
- **Never switch this checkout to an upstream-based branch.** Such a branch has none of the fork's files; on the share that once left a folder half-deleted and the checkout stuck on the wrong branch. Use a worktree.

## Log

Newest first.

- **2026-10-11 (fork rules, pull request drafts):** The fork's rules moved to `AGENTS-aniefork.md` at the root, as in the sister forks arcanii/wordcraft and arcanii/gridcraft (they were in `.claude/rules/aniefork.md`, which made git remove and recreate `.claude/` on every switch to an upstream-based branch). This file gained the sister forks' sections: the upstream files each change touches, how a pull request is made, and building. Three pull requests were drafted from `upstream/main` (`5d59060`) for review, none opened: the minimum Rust version, the XFA script time limit, and the scripts-off notice.
- **2026-10-10 (merge of upstream 0.5.0):** Merged 180 upstream commits (`0bc619c`). The code merged cleanly; `ROADMAP.md`, `ATTRIBUTION.toml` and `parity/acrobat-features.toml` conflicted and were resolved by hand. Upstream had added catalog coverage tests for German, Italian, Brazilian Portuguese, Ukrainian, Bulgarian, Hungarian and Arabic, so the fork's "scripts are turned off" notice was translated in those catalogs too. 1.95 is still the real minimum Rust version. This file was started, and `ROADMAP.md` went back to upstream's text.
- **2026-10-10 (M12, XFA scripts turned off):** When a runaway script turns an XFA form's scripts off, the app now says so: a toast as it happens (at open or after a click), and the notice bar from then on, with a JavaScript Console button (the error was only in the console, so calculations and buttons just stopped). The engine reports the state (`Document::xfa_scripts_off`, and `xfa_scripts_off` in `doc_info` for agents) instead of the UI reading error text. The notice bar's text now wraps beside its buttons instead of running under them (the French sentence did). The sentence is translated in 13 catalogs; Telugu and Czech show it in English.
- **2026-10-09 (M12, XFA script time limits):** An XFA script's time limit (1 s for scripts that run on open) counted the engine's start-up too: a new thread and context, plus the engine's own set-up for the first script in a process (≈ 60–170 ms when idle). On a busy machine a trivial initialize script could pass the limit, be abandoned and turn the form's scripts off (the cause of the flaky `xfa.rs` UI tests: they failed 8 of 8 runs under full CPU load). The limit now runs from when the engine is ready; start-up has its own 10 s cap, and a trivial warm-up script takes the first-in-process set-up. Under the same load: 24 of 24 runs pass. Runaway scripts are still abandoned as before.
- **2026-10-09 (M0, minimum Rust version):** The workspace `rust-version` said 1.90, but egui 0.36 needs 1.95 and rten 0.26 needs 1.94, so older toolchains failed with a dependency error. It now says 1.95, checked by building every target with 1.95.0 (1.94.0 is refused); the README's Get started names it. Every crate inherits it (11 of 34 did, so the app, CLI and core crates declared no minimum).
