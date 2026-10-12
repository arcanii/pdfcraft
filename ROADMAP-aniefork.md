# PdfCraft aniefork roadmap

This is the fork's own record: [arcanii/pdfcraft](https://github.com/arcanii/pdfcraft), a fork of [storytold/pdfcraft](https://github.com/storytold/pdfcraft).

Upstream's [ROADMAP.md](ROADMAP.md) is kept **identical to upstream** here. Both sides used to add log lines at the top of that file, so it conflicted on every merge. Work done in the fork is logged in this file instead.

- **Fork work:** add a line to the log below. Do not edit `ROADMAP.md`.
- **A branch meant as an upstream pull request:** follow upstream's rules there (`CLAUDE.md`), including its `ROADMAP.md` log line. Cut the branch from `upstream/main`, not from the fork's `main`.

The fork's rules are in [AGENTS-aniefork.md](AGENTS-aniefork.md); the sister forks arcanii/wordcraft and arcanii/gridcraft keep the same two files.

## Sync status

| | |
|---|---|
| Last merge of upstream | 2026-10-12, upstream `4701a00` (PdfCraft 0.6.0), merge commit `e1b5551` |
| Fork is behind upstream by | 0 commits at that merge |
| Fork carries | 1 change upstream lacks (below) |

## What the fork carries

Changes in the fork that upstream does not have. Update the last column when one is offered or merged; delete the row once a merge of upstream brings it back.

| Change | Fork commits | Upstream |
|---|---|---|
| The app says when a form's scripts were turned off: a toast, then the notice bar with a JavaScript Console button, and `xfa_scripts_off` in `doc_info`. | `03b2789`, with translations added in the merges `e4b2f7b` and `e1b5551` | Offered 2026-10-12: [storytold/pdfcraft#1251](https://github.com/storytold/pdfcraft/pull/1251), open. One commit on upstream `84a75ea`, branch `xfa-scripts-off-notice`. |

No longer carried, because upstream has them:

- **The XFA script time limit:** the fork's change, merged upstream as [storytold/pdfcraft#1081](https://github.com/storytold/pdfcraft/pull/1081) on 2026-10-11 (`a7e24b9`).
- **The minimum Rust version:** upstream raised it to 1.95 itself (`75b113a`, #1188). The fork's extra `rust-version.workspace = true` in every crate was dropped at the merge of 2026-10-12, so the manifests are upstream's.

## Changes in upstream files

The change the fork carries is a fix to upstream's own code, edited in place in upstream's style, because it is meant as a pull request (the exception in `AGENTS-aniefork.md`). After merging upstream, check these are still there. When upstream takes it, the rows go.

| Change | Upstream files it touches |
|---|---|
| Scripts-off notice | `crates/engine/src/lib.rs` (`xfa_scripts_off`: the field, its accessor, and where `ran_away` is handled), `crates/automation/src/lib.rs` (`doc_info`), `crates/ui-egui/src/js_ui.rs` (`handle_js`, `scripts_off_notice`), `crates/ui-egui/src/canvas.rs` (`DocView::scripts_off_noted`, `Notice::Console`, and in `notices` the message ahead of the others and the JavaScript Console button inside `notice_bar`'s closure), their tests (`crates/ui-egui/tests/ui_group/xfa.rs`, `crates/engine/src/tests.rs`, `crates/automation/tests/automation.rs`), one sentence in 17 catalogs under `crates/ui-egui/src/i18n/`, those catalogs' hashes in `ATTRIBUTION.toml`, and `form.xfa-dynamic` in `parity/acrobat-features.toml` |

This is where the fork departs from its own rules: a fork-private feature would keep its text in a table of its own and leave the catalogs, `ATTRIBUTION.toml` and `parity/` alone. Those three are what conflicts when upstream is merged, and every interface language upstream adds with a coverage test needs the sentence translated before the merge passes.

## Syncing with upstream

```sh
git fetch upstream
git merge upstream/main
```

Sync often: upstream lands dozens of commits a day, and small merges are easy. With `ROADMAP.md` out of the way, these still conflict when both sides touch them:

- **`ATTRIBUTION.toml`:** each translation catalog's SHA-256 is recorded there. When both sides changed a catalog, take upstream's entry, then recompute the hash of the merged file (`cargo xtask assets` lists the stale ones).
- **`parity/acrobat-features.toml`:** when both sides edited the same feature. Take upstream's text and re-apply the fork's addition.
- **New catalog coverage tests:** upstream adds interface languages with a test that every `tl!("…")` string is translated. A string the fork added must then be translated in the new catalog (`cargo test -p pdfcraft-ui-egui --lib i18n` names what is missing).

- **Moved files:** when upstream moves a test file, git carries the fork's tests along, but a path the fork cites in `parity/acrobat-features.toml` still names the old place (`cargo xtask parity` says which).

After a merge, run `cargo xtask ci` before committing it (with `CARGO_TARGET_DIR` on a local disk it takes about ten minutes), and update the tables above.

## Sending changes upstream

Only when the owner asks for it, one topic per pull request. How the sister forks do it (storytold/gridcraft#265 and #266, storytold/wordcraft#570 and #571), which pdfcraft follows:

- **A branch cut from `upstream/main`, in a separate worktree** (never by switching this checkout): `git worktree add -b <branch> <local dir> upstream/main`. A worktree on a local disk whose repository is on the network share needs `GIT_CONFIG_COUNT=1 GIT_CONFIG_KEY_0=safe.directory GIT_CONFIG_VALUE_0=<worktree>` in the environment of every git command (nothing is written to the global configuration). Branch names are short and plain: `xfa-script-time-limit`.
- **Upstream's style, no `aniefork` anywhere,** and upstream's documents updated as its `CLAUDE.md` and `AGENTS.md` ask: a line at the top of the `ROADMAP.md` log, `parity/acrobat-features.toml` when a feature changes, interface text through `tl!` with an entry in every catalog that has a coverage test, and the catalogs' hashes in `ATTRIBUTION.toml`.
- **Contributor names:** `[people.arcanii]` in `contributors/people.toml`, as its own commit ("Add arcanii to the contributor names"), the same entry as in the sister forks. It went in with #1081, so upstream has it and later pull requests need nothing.
- **The pull request:** the title is the commit title upstream will keep when it squashes (`M12: what changed`, upstream's task-id rule; the sister projects use `Area: what changed`). The body has What was wrong, What changed and Checks (for a feature: What it does, How, Not in this pull request, Checks), says honestly which checks ran locally and on which upstream commit, and names what was left to CI. Pushed to `origin` and opened with `gh pr create --repo storytold/pdfcraft --base main --head arcanii:<branch>`.
- **Building the branch:** when the fork's `main` already holds the change merged with a recent upstream, start the branch at that upstream commit, take the changed files from `main` (`git checkout main -- <files>`, never the `-aniefork` documents), add upstream's progress-log line, commit, and rebase onto `upstream/main`. That reuses a state the gate has already passed on.
- **Before opening:** search upstream for a duplicate (it closes the later one), and refresh the branch. Upstream adds a line at the top of `ROADMAP.md` with every commit, so a branch conflicts there within hours.
- **Upstream's progress documents moved on 2026-10-10:** the log is now `ROADMAP.md` ▸ Progress log (a line at the top, ending with the new overall estimate or "Overall unchanged"), the milestone table is `docs/roadmap.md`, and a gap that closes comes out of `docs/gaps.md`.
- **Checks:** run `cargo xtask ci` in the worktree with `CARGO_TARGET_DIR` on the local disk and `WGPU_BACKEND=dx12` (fmt, clippy, tests, layers, wasm, assets, deny, parity). Measure a before-and-after claim on the same upstream commit, in alternating rounds: a flaky failure varies a lot from round to round, and one round proves little. Give each worktree a `CARGO_TARGET_DIR` of its own: cargo reuses a crate compiled in another worktree, with that worktree's path compiled in, and `xtask`'s checks then read the wrong tree.
- **After upstream merges it:** it squashes, so the fork's own commits differ from upstream's. Merge upstream, take its version where they overlap, and take the change out of the tables above.

What upstream's merged pull requests look like, as of 2026-10-10: 88 of the last 100 came from forks; one maintainer merges, usually within hours, sometimes pushing a small follow-up; CI runs the tests on Linux and Windows, the wasm32 check and the dependency licence check, and a first-time contributor's CI waits for approval. There is no `CONTRIBUTING.md`, pull request template or CLA.

## Building

- **Rust:** 1.95 or newer (egui 0.36); upstream declares it since `75b113a`. `cargo xtask ci` also needs the `wasm32-unknown-unknown` target and `cargo-deny`.
- **The UI tests** render through wgpu. On a machine with an NVIDIA card and an integrated GPU, run them with `WGPU_BACKEND=dx12`: egui_kittest then picks the software adapter.
- **This checkout is on a network share:** builds are slow, and a transient "can't find crate" (E0463) only needs a rerun. A worktree and its `CARGO_TARGET_DIR` on a local disk are much faster.
- **Never switch this checkout to an upstream-based branch.** Such a branch has none of the fork's files; on the share that once left a folder half-deleted and the checkout stuck on the wrong branch. Use a worktree.

## Log

Newest first.

- **2026-10-12 (second pull request to upstream):** The scripts-off notice was offered as [storytold/pdfcraft#1251](https://github.com/storytold/pdfcraft/pull/1251): one commit on upstream `84a75ea`, built from the state the fork's `main` already had after the merge and rebased over the six commits upstream had added since (only `ATTRIBUTION.toml` conflicted). `cargo xtask ci` passed on it (2,647 tests). Upstream's CI started at once this time. With it, everything the fork carries has been offered upstream.
- **2026-10-12 (merge of upstream 0.6.0):** #1081 was merged upstream on 2026-10-11, unchanged, a few hours after it was opened. Merged 213 upstream commits (`4701a00`) into the fork as `e1b5551`. Two of the three carried changes are now upstream's: the time limit (#1081), and the minimum Rust version, which upstream raised itself; the fork's manifests and README went back to upstream's. `canvas.rs` conflicted as expected (upstream's `notice_bar`), `engine/src/lib.rs` trivially, and `ATTRIBUTION.toml` and `parity/` as always. Upstream had added coverage tests for Turkish, Czech, Slovene and Kazakh, so the notice was translated there, and had moved the UI tests into `tests/ui_group/`, which the parity evidence had to follow. `cargo xtask ci` passes on the merged tree (2,568 tests).
- **2026-10-11 (first pull request to upstream):** The XFA script time limit was offered as [storytold/pdfcraft#1081](https://github.com/storytold/pdfcraft/pull/1081), from the branch `xfa-script-time-limit` rebased onto upstream `bbbe600` (0.6.0), with the contributor entry as its own commit. `cargo xtask ci` passed on it (2,132 tests). Measured on that commit under full CPU load, in alternating rounds: 25 of 40 runs of the `xfa` test binary fail without the change and none of 40 with it (the earlier "8 of 8" was one round, and too strong). The minimum Rust version is not being offered (the owner's decision). The scripts-off notice was rebased as well (`cargo xtask ci` passes, 2,135 tests) and waits for #1081; upstream had fixed the notice bar's wrapping itself in the meantime (#832), so that part came out of the draft.
- **2026-10-11 (fork rules, pull request drafts):** The fork's rules moved to `AGENTS-aniefork.md` at the root, as in the sister forks arcanii/wordcraft and arcanii/gridcraft (they were in `.claude/rules/aniefork.md`, which made git remove and recreate `.claude/` on every switch to an upstream-based branch). This file gained the sister forks' sections: the upstream files each change touches, how a pull request is made, and building. Three pull requests were drafted from `upstream/main` (`5d59060`) for review, none opened: the minimum Rust version, the XFA script time limit, and the scripts-off notice.
- **2026-10-10 (merge of upstream 0.5.0):** Merged 180 upstream commits (`0bc619c`). The code merged cleanly; `ROADMAP.md`, `ATTRIBUTION.toml` and `parity/acrobat-features.toml` conflicted and were resolved by hand. Upstream had added catalog coverage tests for German, Italian, Brazilian Portuguese, Ukrainian, Bulgarian, Hungarian and Arabic, so the fork's "scripts are turned off" notice was translated in those catalogs too. 1.95 is still the real minimum Rust version. This file was started, and `ROADMAP.md` went back to upstream's text.
- **2026-10-10 (M12, XFA scripts turned off):** When a runaway script turns an XFA form's scripts off, the app now says so: a toast as it happens (at open or after a click), and the notice bar from then on, with a JavaScript Console button (the error was only in the console, so calculations and buttons just stopped). The engine reports the state (`Document::xfa_scripts_off`, and `xfa_scripts_off` in `doc_info` for agents) instead of the UI reading error text. The notice bar's text now wraps beside its buttons instead of running under them (the French sentence did). The sentence is translated in 13 catalogs; Telugu and Czech show it in English.
- **2026-10-09 (M12, XFA script time limits):** An XFA script's time limit (1 s for scripts that run on open) counted the engine's start-up too: a new thread and context, plus the engine's own set-up for the first script in a process (≈ 60–170 ms when idle). On a busy machine a trivial initialize script could pass the limit, be abandoned and turn the form's scripts off (the cause of the flaky `xfa.rs` UI tests: they failed 8 of 8 runs under full CPU load). The limit now runs from when the engine is ready; start-up has its own 10 s cap, and a trivial warm-up script takes the first-in-process set-up. Under the same load: 24 of 24 runs pass. Runaway scripts are still abandoned as before.
- **2026-10-09 (M0, minimum Rust version):** The workspace `rust-version` said 1.90, but egui 0.36 needs 1.95 and rten 0.26 needs 1.94, so older toolchains failed with a dependency error. It now says 1.95, checked by building every target with 1.95.0 (1.94.0 is refused); the README's Get started names it. Every crate inherits it (11 of 34 did, so the app, CLI and core crates declared no minimum).
