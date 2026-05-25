# shawn-wrangle

Give it a goal — rough notes, a brief, files, links, a voice-memo transcript, whatever — and it builds a focused Prompt Kit: numbered prompts in the right order, each scoped to one task, each assigned to the cheapest model that can do the job correctly.

## What it does

Paste your goal in one message. shawn-wrangle extracts what it needs from whatever you give it, then applies three decomposition tests to break the work into the smallest correct set of steps:

- **Single-output test** — if a prompt would produce two things, it splits into two prompts
- **Prior-resolution test** — if a step depends on an unknown, that unknown becomes its own earlier step
- **Scope-creep test** — every "and also" and "as well as" is a hidden second task; each one gets its own prompt

Before building anything, it shows you a plain-English plan: what each prompt does, what model it uses, what it depends on. You approve or edit, then it builds.

The kit comes out in a standard format — each prompt has a role, explicit inputs, a single scoped task, a hard scope boundary, and an output label. Run them yourself, or have shawn-wrangle run them for you inside Claude Code.

**Three ways to run:**

- **BUILD** — delivers the kit and a step-by-step guide for running each prompt yourself in claude.ai
- **BUILD-RUN** — runs the kit in Claude Code, pausing for your approval after each group
- **BUILD-RUN-AUTO** — runs end-to-end, validates each output, halts and reports on any failure

After an in-session run, it offers to save the kit as a reusable skill.

## Requirements

- [Claude Code](https://claude.ai/code)

## How to install

```bash
git clone https://github.com/kgsubs/shawn-wrangle ~/.claude/skills/shawn-wrangle
```

Claude Code loads skills from `~/.claude/skills/` automatically on next launch.

**Use it:**

```
/shawn-wrangle
```

Or describe a multi-step goal and it will activate on its own.

## Examples

**Messy input, personal project:**

```
i have an album i've been working on for two years. 14 demos, some basically
done, some are voice memos with chords on my phone. i need to sequence it,
write a mix brief for my collaborator, figure out distribution, write a press
bio. i have no idea what i'm doing, budget is zero.
```

shawn-wrangle extracts five workstreams, flags the dependency chain (sonic vision must be defined before the mix brief; track sequencing can't start until completion status is assessed), and produces a kit of 8 prompts. Haiku handles track status extraction from the notes doc; sonnet handles everything requiring judgment.

**Structured brief, business context:**

```
We're launching a new product line. I need retail pricing, a margin model
for our DTC channel, a one-page brief for the contract manufacturer, and a
competitive positioning summary. I have a cost breakdown and three competitor
SKU lists.
```

shawn-wrangle flags four separate workstreams, sequences them so the margin model runs before pricing is set, registers the cost sheet and competitor files as named resources with step assignments, and builds a 4-prompt kit with explicit dependencies.

## Customization

**Different slash command:** rename the directory and update the `name:` field in `SKILL.md`:

```bash
mv ~/.claude/skills/shawn-wrangle ~/.claude/skills/wrangle
# edit SKILL.md: change `name: shawn-wrangle` to `name: wrangle`
```

**Output path for in-session runs:** kit outputs default to `~/.cache/prompt-mage/runs/`. Edit the working dir in Stage 5b of `SKILL.md` to change it.

## License

MIT
