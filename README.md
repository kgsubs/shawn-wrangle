# shawn-wrangle

Turn any goal, messy or clean, into a results-focused prompt kit that gets it done: numbered prompts pointed at specific outputs, in the right order, each assigned to the cheapest model that can do the job.

## What it does

Paste your goal in one message. Rough notes, a structured brief, links, files, a voice-memo transcript, whatever you have. shawn-wrangle figures out what needs to happen and in what order, then builds you a kit you can actually run.

Three tests make sure every step earns its place:

- **Single-output test:** if a prompt would produce two things, it splits into two prompts
- **Prior-resolution test:** if a step depends on an unknown, that unknown becomes its own earlier step
- **Scope-creep test:** every "and also" and "as well as" is a hidden second task; each one gets its own prompt

Before building anything, it shows you a plain-English plan: what each prompt produces, what model it uses, what it depends on. You approve or edit, then it builds.

Each prompt in the kit has a role, explicit inputs, a single scoped task, a hard scope boundary, and a named output. Run them yourself, or have shawn-wrangle run them for you inside Claude Code.

**Three ways to run:**

- **BUILD:** delivers the kit and a step-by-step guide for running each prompt yourself. Works in Claude Code and Claude Chat.
- **BUILD-RUN:** runs the kit in Claude Code, pausing for your approval after each group. Claude Code only.
- **BUILD-RUN-AUTO:** runs end-to-end, validates each output, halts and reports on any failure. Claude Code only.

After a BUILD-RUN or BUILD-RUN-AUTO run, it offers to save the kit as a reusable skill.

## Requirements

**Claude Code:**

- [Claude Code](https://claude.ai/code)

**Claude Chat:**

- Pro, Max, Team, or Enterprise plan
- Code execution enabled in your account

## How to install

**Claude Code:**

```bash
git clone https://github.com/kgsubs/shawn-wrangle ~/.claude/skills/shawn-wrangle
```

Claude Code loads skills from `~/.claude/skills/` automatically on next launch.

**Claude Chat:**

1. Download this repo as a zip file (Code > Download ZIP on GitHub)
2. Go to Settings > Features > Skills
3. Upload the zip file

Skills installed in Claude Code and Claude Chat are separate and do not sync.

**Use it:**

```
/shawn-wrangle
```

Or describe a multi-step goal and it will activate on its own.

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

shawn-wrangle extracts five workstreams, flags what blocks what (sonic vision before mix brief; completion status before sequencing), and produces a kit of 8 prompts each pointed at a concrete deliverable. Haiku handles track status extraction; sonnet handles everything requiring judgment.

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

shawn-wrangle identifies four workstreams, sequences them so the margin model runs before pricing is set, registers the cost sheet and competitor files as named resources, and produces a 4-prompt kit where every dependency is explicit.

## Customization

**Different slash command:** rename the directory and update the `name:` field in `SKILL.md`:

```bash
mv ~/.claude/skills/shawn-wrangle ~/.claude/skills/wrangle
# edit SKILL.md: change `name: shawn-wrangle` to `name: wrangle`
```

**Output path for in-session runs:** kit outputs default to `~/.cache/prompt-mage/runs/`. Edit the working dir in Stage 5b of `SKILL.md` to change it.

## License

MIT
