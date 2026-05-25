# shawn-nuclear

One goal, many prompts. Dependency-mapped, model-assigned, and runnable end-to-end in Claude Code.

Works in Claude Code (full execution modes) and Claude Chat (BUILD mode, kit delivered for manual run).

## What it does

Paste your goal. Raw notes, a brief, files, links, a transcript -- whatever you have. shawn-nuclear reads it, decomposes it into a sequenced prompt kit, and either hands it to you or runs it.

Every proposed step gets checked against three questions: does it produce exactly one thing? does it depend on something unresolved? is it secretly two tasks joined by "and also"? Anything that fails gets split or sequenced correctly. Dependencies are made explicit before anything runs.

**Three modes:**

- **BUILD** -- produces the kit with a step-by-step guide for running each prompt yourself. Claude Code and Claude Chat.
- **BUILD-RUN** -- runs the kit in Claude Code, pausing for your approval after each group. Claude Code only.
- **BUILD-RUN-AUTO** -- runs end-to-end, validates each output, halts and reports on failure. Claude Code only.

After BUILD-RUN or BUILD-RUN-AUTO, it offers to save the kit as a reusable skill.

## Requirements

**Claude Code:**

- [Claude Code](https://claude.ai/code)

**Claude Chat:**

- Pro, Max, Team, or Enterprise plan
- Code execution enabled

## Install

**Claude Code:**

```bash
git clone https://github.com/kgsubs/shawn-nuclear ~/.claude/skills/shawn-nuclear
```

Claude Code loads skills from `~/.claude/skills/` automatically on next launch.

**Claude Chat:**

1. Download this repo as a zip file (Code > Download ZIP on GitHub)
2. Go to Settings > Features > Skills
3. Upload the zip file

Skills installed in Claude Code and Claude Chat are separate and do not sync.

**Use it:**

```
/shawn-nuclear
```

Or describe a multi-step goal and it triggers on natural language.

## Examples

**Messy input, personal project:**

```
i have an album i've been working on for two years. 14 demos, some basically
done, some are voice memos with chords scribbled on my phone at 2am that i
can barely read now. the vibe is kind of slow and heavy, post-folk electronic,
i think, but i honestly struggle to describe it. my friend ana is mixing it
but she keeps asking me for a mix brief and i don't really know what that
means or what to put in it. i need to figure out the track order, write
something useful for her, sort out mastering (no clue), get it on streaming,
and write a bio for my bandcamp page. i have a google doc with notes and
half-finished lyrics for each track but it's pretty chaotic. i've never
released anything before. budget is basically zero.
```

shawn-nuclear extracts five workstreams, flags what blocks what (sonic vision before mix brief; completion status before sequencing), and produces a kit of 8 prompts each pointed at a concrete deliverable. Haiku handles track status extraction; Sonnet handles everything requiring judgment.

**Structured brief, business context:**

```
We're launching a new personal care product line in Q3. I need retail pricing
across three channel tiers, a contribution margin model for our DTC channel
that accounts for CAC and a realistic return rate, a one-page brief for our
contract manufacturer covering formulation specs and MOQ expectations, and a
competitive positioning summary for the sales deck. I also need to figure out
whether we lead with the hero SKU or launch the full range at once. I have a
cost breakdown by SKU, three competitor price lists scraped last week, and a
category analysis we commissioned in January. We go to market in 14 weeks.
```

shawn-nuclear identifies four workstreams, sequences them so the margin model runs before pricing is set, registers the cost sheet and competitor files as named resources, and produces a 4-prompt kit where every dependency is explicit.

## License

MIT
