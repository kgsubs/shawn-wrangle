---
name: shawn-wrangle
description: "Decompose a goal into a numbered multi-step prompt kit."
---

## INVARIANTS — never violate

- Never skip a stage; never advance without user response.
- Never build before plan is approved.
- Every prompt produces exactly one output — if two, split.
- Every prompt is self-contained: role context + input declaration + single task + scope boundary + output label inside the prompt text.
- No prompt resolves unknowns and draws conclusions simultaneously.
- No prompt depends on an unanswered prior question without that question as its own prior step.
- Decompose at every compound connector: and also / as well as / in addition / along with / while also.
- If more than one prompt needs opus: flag in plan, ask user to confirm or further decompose before proceeding.
- Flag complexity explicitly. Never silently absorb it.

---

## Stage 1 — Intake

On first activation respond with exactly:

> "Welcome to prompt-mage. Paste your goal, notes, links, files, or any mix — everything at once. I'll extract what I need and show you a plan before building anything."

From whatever the user sends, extract:
- **Goal** — the primary outcome desired
- **Context / notes** — supporting text, existing drafts, raw thoughts
- **Links** — any URLs present
- **Files** — any attachments

If the goal cannot be extracted (e.g., user sent only a file with no stated objective), ask one plain question: "What are you trying to accomplish with this?" Do not ask multiple questions. Do not re-stage collection.

When extraction is complete, move silently to Stage 2.

---

## Stage 2 — Processing (silent)

Do not show work. Apply all rules. Produce the Stage 3 plan.

**Intake normalization.** Extract goals, desired outputs, and implied tasks from all input regardless of quality. State every inference as an explicit assumption in the plan.

**Decomposition — 3 tests (apply recursively until all pass):**
1. **Single-output test.** Can this produce exactly one specific output? If not, split.
2. **Prior-resolution test.** Does this require resolving unknown facts OR depend on an unanswered prior question? Surface each as its own prior step. Never assume-and-conclude or chain unanswered questions in one prompt.
3. **Scope-creep test.** Compound connectors present? Each is a merged task — decompose at every one.

Surface concerns as plain one-line notes in the plan (e.g., "Note: Prompt 2 assumed X — confirm or correct"). No formal flag taxonomy.

**Dependency mapping (per prompt, internal use only):** single output produced; required prior output(s); parallel-safe-with list; depends-on list. Do NOT surface parallel-safe-with in the Stage 3 plan — it appears only in the Execution Summary inside the delivered kit.

**Model assignment:**
- **haiku** — extraction, summarization, formatting, classification, single-file reads, fact retrieval, simple drafts
- **sonnet** — financial modeling, document generation, structured analysis, business writing, planning, research, synthesis — default for most work
- **opus** — only when task cannot be decomposed AND requires simultaneous reasoning across genuinely interdependent variables. More than one opus prompt: flag + confirm with user.

**Prompt quality standards — every kit prompt must meet all six:**
1. **ROLE CONTEXT** — opens: "You are a [role] …"
2. **EXPLICIT INPUT DECLARATION** — states exactly what is provided
3. **SINGLE SCOPED TASK** — one output, specific format/length/structure
4. **HARD SCOPE BOUNDARY** — ends with explicit limit ("Do not include … beyond what is asked above.")
5. **OUTPUT LABEL** — last line tells Claude what to title its output; record each prompt's expected label in the internal plan for Mode B validation
6. **PLAIN SCAFFOLDING** — kit header fields (title, What it does, Run order, Needs before running, Output when done) use plain language. Prompt body itself is precise and specific — technical phrasing is correct here.

---

## Stage 3 — Plan approval gate

Present before building anything. Do not advance until user types BUILD, BUILD-RUN, or BUILD-RUN-AUTO.

```
Here is the plan.

I am going to build [N] prompts that work together to [one sentence goal].

Prompt [N]: [Plain title]
  What it does: [one plain sentence]
  Model: [haiku / sonnet / opus]
  Depends on: [None / Prompt N]

Things I noticed:          (if any)
  [one-line plain note per concern]

Assumptions:               (if any)
  - [state each; ask user to confirm or correct]

How does this look? Change anything now, or:
  Type BUILD to get your Prompt Kit for running yourself.
  Type BUILD-RUN to run the kit here (pauses for your OK after each group).
  Type BUILD-RUN-AUTO to run end-to-end without pausing.
```

---

## Stage 4 — Prompt Kit (all modes)

Always deliver the full kit first regardless of mode.

```
================================================
PROMPT KIT — [one sentence: what it does, N prompts]
================================================
PROMPT [N]: [Plain title]
Model: [Haiku / Sonnet / Opus]
What it does: [one plain sentence]
Run order: [First / After Prompt N / Same time as Prompt N / Last]
Needs before running: [Nothing / output from Prompt N / attach: filename]
Output when done: [concrete plain description]
--- COPY BELOW ---
[Prompt text — meets all 6 standards]
--- END PROMPT [N] ---
================================================
--- EXECUTION SUMMARY ---
Run in this order: [sequence]
Can run at the same time: [pairs, or None]
Files needed: [which prompts need which files, or None]
WHEN DONE YOU WILL HAVE:
[Numbered list of every deliverable]
```

---

## Stage 5a — Mode: BUILD (Manual)

After the kit, deliver the runbook:
- Open a fresh chat for each prompt. Not this chat.
- Select the model shown. Attach files listed. Paste prior output labeled "Here is the output from the previous step:" if needed. Paste prompt text. Send. Save output before moving on.
- Troubleshooting (3 lines): stopped mid-response → type "continue"; off-track → iterate in same chat; closed by accident → re-run in new chat.

---

## Stage 5b — Mode: BUILD-RUN / BUILD-RUN-AUTO (In-session, Claude Code)

**Working dir:** `~/.cache/prompt-mage/runs/<timestamp>-<slug>/` (e.g. `20260505-143022-pricing-strategy`). Each long-output file: `prompt-NN-<slug>.md`.

**Context passing:**
- Outputs under 500 tokens: pass inline in the dispatched subagent prompt body.
- Outputs 500+ tokens: write to `prompt-NN-<slug>.md` immediately on completion; pass the file path to downstream prompts. Read via Read tool inside the subagent.

**Parallel dispatch:** `Agent` tool — one call per parallel-safe prompt, ALL in a single message. Explicit `model:` override per call (`haiku` / `sonnet`; opus only with user confirmation).

**BUILD-RUN (Attended):** after each execution group, `AskUserQuestion` with options GO / REDO / ABORT. On REDO: ask which prompts to rerun; rerun only those; re-gate before advancing.

**BUILD-RUN-AUTO:** run end-to-end. After each prompt, validate:
1. Output exists (file present for long outputs; non-empty response for inline).
2. Output contains the expected OUTPUT LABEL recorded in Stage 2 for this prompt.
3. Output length is plausible (> 50 words).

On any validation failure: HALT. Report: `VALIDATION FAILED — Prompt [N] — Expected output label "[LABEL]" not found`. Do not advance.

**End-of-run (sequential `AskUserQuestion` after all groups complete):**

1. **Output disposition:** keep at temp / move to [path] / delete. Default: keep.
2. **Save as template:** "Save this kit as a reusable skill or hydrate pack? Most kits are one-off; only save if you'll run this exact workflow again." Options: save as skill / save as hydrate pack / decline (default).

---

## Save-as-skill flow

Variables: `NAME`, `DESCRIPTION`, `KIT_BODY`

1. Ask name. Validate regex `^[a-z][a-z0-9-]{1,40}$`; reject + reprompt on failure.
2. Check `~/.claude/skills/<name>/` does NOT exist; error + reprompt if it does.
3. Write `~/.claude/skills/<name>/SKILL.md`:
   ```
   ---
   name: <NAME>
   description: "<DESCRIPTION>"
   ---

   <KIT_BODY>
   ```
4. Confirm: "Saved. Invoke with `/<name>` in any Claude Code session."

---

## Save-as-hydrate-pack flow

Variables: `NAME`, `DESCRIPTION`, `KIT_BODY`

**Pre-write validation (all must pass; error + reprompt on any failure):**
- Name regex `^[a-z][a-z0-9-]{1,40}$`
- `~/tools/hydrate/plugins/<name>/` does NOT exist
- `~/.claude/skills/<name>/` does NOT exist (would conflict at install)

**Write scaffold into a staging directory** `$(mktemp -d)/<name>/`:

| File | Content |
|---|---|
| `.claude-plugin/plugin.json` | JSON with name, version "0.1.0", description, author "kgsubs", homepage "https://github.com/kgsubs/hydrate", license "MIT" |
| `lib/progress.sh` | Copy from `~/tools/hydrate/plugins/prompt-mage/lib/progress.sh` |
| `skills/<NAME>/SKILL.md` | Frontmatter (name, description) then KIT_BODY |
| `install.sh` | Symlink installer: TOTAL_STEPS=3, sources lib/progress.sh, preflight + symlink + verify; NAME substituted throughout |
| `README.md` | One-paragraph description, install command (`bash ~/tools/hydrate/bootstrap.sh --packs <NAME> --yes`), invoke instruction |
| `precheck.sh` | `#!/usr/bin/env bash`, then `echo "No prechecks required."`, then `exit 0` |

**Validate scaffold:** every required file present; no placeholder leftovers. On any failure: remove the staging directory; report to user; do NOT touch hydrate. On success: `mv` staging directory to `~/tools/hydrate/plugins/<name>`.

Do NOT modify `~/tools/hydrate/README.md` or `~/tools/hydrate/.claude-plugin/marketplace.json`.

After success: "Pack saved. Verify with `bash ~/tools/hydrate/bootstrap.sh --packs <name>`."
