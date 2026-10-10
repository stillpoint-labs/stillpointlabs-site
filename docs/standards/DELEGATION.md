# Standard: Delegation Hygiene

*Cross-project standard. Canonical source for the "Delegation hygiene" block in `~/.claude/CLAUDE.md` and the
gates enforced in `borg-collective/agents/borg-nanoprobe.md`. Documentation only — not auto-loaded; `@`-include
or summarize it where a repo's CLAUDE.md or a skill needs the rules.*

## Why this exists

A delegated build-task once ran **15 reads / 0 writes for 37 minutes**, then its final message exceeded the
32k output ceiling, the API aborted, and it returned **nothing** on an empty branch. Root cause: an over-broad
brief + a flaky optional step + no write-first rule + no output cap. The orchestrator believed work had landed
when none had. This standard prevents that failure class on **both sides** — the orchestrator that writes the
brief (O-rules) and the worker that executes it (W-rules) — because instruction on one side alone is
rationalizable.

## O — Orchestrator rules (check BEFORE dispatching any sub-agent / nanoprobe build task)

- **O1 — One deliverable per dispatch.** One module OR one test suite OR one harness — never "X + tests +
  harness + optional Y" in a single brief. `>4 files` or `>~300 new lines` ⇒ split into sequenced spawns.
- **O2 — Name every file the agent must write** (absolute paths). No file list ⇒ no dispatch.
- **O3 — No optional steps.** Ban "optional / if time / bonus / as needed." A flaky optional step (e.g. a prod
  export) is a spinning anchor — make it a *separate* spawn fired only on success of the main task.
- **O4 — Bound the reads.** State `Read only: [files]; if you need one not listed, stop and report.`
- **O5 — Fail-first ordering.** Put any step that can block later steps first, with `if it fails, report and
  exit — do not proceed.`
- **O6 — Mandate the return format inline.** `Final message ≤200 words: files written, verification result,
  blockers. No code, no diffs, no raw output.` Don't rely on the agent definition alone.
- **O7 — Bound every fan-out / retry loop.** Declare an explicit ceiling (max spawns or iterations) up front
  and hard-exit with a failure summary when hit. Open-ended loops are a protocol violation.

## W — Worker rules (enforced in the agent definition; the receiver-side backstop)

- **W1 — Scope gate.** Read the Task first. If it names `>2` primary deliverables or contains an optional
  keyword, do NOT begin — reply `SCOPE-EXCEEDED: brief names [N]. Split into: [A], [B].` and exit.
- **W2 — Bounded reading.** Read only what the task needs; after **5** Read/Grep/Glob calls you MUST produce a
  Write or Edit before reading further. Reading past action 5 with zero writes is a protocol violation.
- **W3 — Write-first.** Flush each unit (function / test / file) to disk immediately; never hold more than
  ~80 lines of new code unwritten.
- **W4 — Commit ratchet.** Commit after each file (`git add <file> && git commit -m "wip: <file>"`). A choke at
  file 5 then leaves 4 recoverable files instead of an empty branch.
- **W5 — No code in the final message.** Ever. No code blocks, diffs, or file contents — code lives on disk,
  referenced by `path:line`.
- **W6 — Overflow fail-safe.** If the final message would exceed ~300 words or contain code: stop, ensure
  everything is committed, and return exactly `PARTIAL: completed [X], skipped [Y]. Code at [paths]. Blockers:
  [Z].` (≤80 words). Never try to emit a whole module/report in one message — it overruns the ceiling and
  returns NOTHING.
- **W7 — Output cap + verify.** A per-agent `CLAUDE_CODE_MAX_OUTPUT_TOKENS` converts a hard abort into a
  truncated-but-returned message; run the project's verification before declaring completion and never declare
  done if it fails.

## Detection (post-hoc, makes a silent failure visible)

The `SubagentStop` hook (`borg-collective/hooks/borg-nanoprobe-log.sh`) scores `last_assistant_message` for
file-path citations **and** flags a **zero-commit** worktree branch (`zero_commit:true` in `agents.jsonl` + a
loud stderr warning). The orchestrator sees the empty-branch failure immediately and re-spawns with a tighter
brief instead of believing the work landed.

## The one-line rule

**Write to disk and commit each file as you finish it; never let your final message carry code — if it would,
commit what you have and return a ≤80-word `PARTIAL:` pointer to the paths.**
