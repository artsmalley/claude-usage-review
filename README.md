# Claude Usage Review

A Claude skill that reviews how you actually use Claude — Claude Code, Cowork, or claude.ai — scores you on a 1–10 maturity scale, and gives you one to three concrete next moves. The advice is the product; the score is the hook.

By [Art Smalley](https://artoflean.com). My other skills, for Lean Thinking and Toyota Production System methods, are at [artsmalley/skills](https://github.com/artsmalley/skills). This one is separate because it is not about lean. It is about the tool.

## Why I built it and how I use it

I use this in the Claude Code CLI on my own machine, every few weeks, to review my usage of the tool and reflect on what to improve next. I say "usage review" and it mines my local data, places me on the scale, and tells me the one or two things that would move me up. Then I go do those things and check again a few weeks later.

That is the same habit TPS applies to a production process: observe the current condition, compare it to a standard, pick the next gap, close it, repeat. The scale is the standard. The next moves are the gap. Applied to how you work with an AI agent instead of how a line runs.

Most people never do this. They install the tool, settle into whatever pattern they fell into the first week, and stay there. A scored review every few weeks is the cheapest way I have found to keep moving.

## What it is designed to do

**It is intentionally broad.** One rubric covers a chat-only professional orchestrating email and Slack through connectors, a developer running subagents and hooks, a creator running a publishing pipeline, and an operations person with a scheduled agent nobody watches. None of those is the "right" way to use Claude. The rubric asks the same four questions of all of them and scores what they actually do.

**It scores four dimensions, then rolls up by judgment.** Not an average. A person is usually better described by their profile than by one number, and the write-up says so.

| Dimension | What it measures |
|---|---|
| Delegation depth | From asking questions, to getting drafts, to handing over whole multi-step tasks with acceptance criteria. |
| Environment investment | CLAUDE.md files, skills, hooks, MCP connections, permission tuning, memory. How much have you built so Claude works better for you specifically? |
| Autonomy granted | Does Claude act only while watched, or run background tasks, scheduled jobs, agents that operate unattended? |
| Output shipped | Has the work left the machine, and does anything depend on it? Weighted by stakes and post-launch iteration, not artifact count. |

**It scores what you do, not what you know.** Knowing about subagents is not using them. In evidence mode this is enforced by the data. In interview mode it is enforced by ten questions built to resist inflation.

## The scale

| Level | Anchor |
|---|---|
| **1–2 · Search substitute** | AI as a better Google. Ask, read, close the tab. Nothing persists between sessions. |
| **3–4 · Copy-paste assistant** | Real drafts, code, and emails, carried to their destination by hand. Redoes rather than steers when output is wrong. Most working professionals are here. |
| **5–6 · Real delegator** | Hands over multi-step tasks end to end. Project context set up. Iterates with the agent. At least one real system connected: files, email, a repo, Slack, Jira. |
| **7–8 · Environment builder** | Custom skills, hooks, MCP, automation. Subagents or scheduled runs. Claude sometimes works unattended. The setup would take a newcomer weeks to replicate. An 8 has at least one thing off the machine that someone else has actually used. |
| **9 · Operator** | Runs something with real stakes — external users, a production workload, or a paying or returning audience — with a live improvement loop: feedback flows in, agent-built updates ship on a visible cadence. Someone would notice an outage. |
| **10 · Production at scale** | Multiple such operations, or one that strangers depend on routinely. Monitoring and recovery exist because they have to. |
| **11 · Off-scale: field shaper** | Not "uses it better." Changes how other people use these tools: published methods others adopt, tooling with real adoption, practices that spread through an organization. You do not self-assess an 11. The field scores you. |

### The levels get harder, and not linearly

This is the part people miss. The curve is exponential, with two step changes.

- **1 through 8 is learnable practice.** Each level is a behavior you can adopt by deciding to. You do not need to be a software engineer. The agent supplies the engineering; you supply delegation judgment and persistence. Moving from 5 to 6 is a matter of weeks of deliberate practice.
- **8 to 9 is where external reality enters.** Something with real stakes has to exist and be operated. That means finding users, or carrying a real workload, and closing the feedback loop so that what comes back changes what ships. You cannot practice your way there alone at a keyboard. This step is harder than everything before it combined.
- **9 to 10 is depth of dependence and scale**, not first contact with the world. Hard, but the same kind of work as 9.
- **10 to 11 is a power-law outcome you do not control.** The field has to adopt your methods. You can only do 11-quality public work and let selection operate.

Because of this, the skill never advises anyone to aim at 11, and next moves always come from one band up, never from the summit. A level-3 person told to "build a skill" bounces off. A level-8 person already did it.

### Stakes, not headcount

"Users" is a proxy. The gate at 9 is an operated system where failure has real cost, in any medium:

- **Software:** external or internal users. Twelve daily users who would notice an outage by lunch outrank five hundred registered accounts that log in quarterly. A Slack bot teammates message, or a triage agent working the Jira queue, qualifies.
- **Infrastructure:** a production workload with zero human users still counts. A backup daemon guarding irreplaceable data, a pipeline moving money. The shrug test: a failure you would shrug at and rerun is convenience; one you would feel for weeks is stakes.
- **Creators and community:** an audience, membership, or revenue stream the pipeline sustains. No engineering skill required. The loop evidence just looks different: cadence held by agents, analytics feeding content decisions, audience feedback visibly changing the product.

## Two modes

**Evidence mode** runs when you are in Claude Code on your own machine. It mines local data with read-only commands: number of projects and sessions, tenure, tool mix across recent transcripts, skills, hooks, MCP servers, permission tuning, CLAUDE.md files, automation, and git history for Claude co-authored commits and deploy configs. It also reads modification times, because environment building is a practice, not an inventory: a CLAUDE.md revised across weeks beats ten stubs created in one afternoon from a blog post.

Privacy: aggregate statistics only. It never reads transcript message bodies. Every command in the skill is listed in `SKILL.md` so you can see exactly what it looks at.

**Interview mode** runs in Cowork, claude.ai, or when you are reviewing someone else. Ten questions, scored from the answers, with the output clearly marked as self-report. Three of the questions are lie detectors:

- A toolchain check. "I built an app" with no git and no deploy target usually means Claude made an HTML file.
- A dependence test. Does anyone use something you made repeatedly, without you involved, and would they notice if it broke tomorrow? Verified dependence overrides a missing toolchain: a tool forty colleagues rely on that IT put on a company server shipped for real.
- A countermeasure ladder. When Claude makes the same mistake twice, what did you do? Gave up, redid it by hand, steered with a better prompt, or changed the system so that class of error cannot recur. Only the last one scores at 7 and above.

If both apply, evidence mode runs and adds two or three interview questions about the surfaces it cannot see.

## What you get back

One screen. No rubric table, no generic tips list.

1. **Score** out of 10 with a one-line characterization in plain words.
2. **Dimension profile**: the four dimensions, each with a score and one line of evidence.
3. **What the evidence shows**: one or two lines of what you are observably doing, stated as fact. No flattery, no deficit framing.
4. **Next moves**: exactly one to three, targeting your weakest dimension, each concrete enough to do this week. "Write a CLAUDE.md for your main project." "Connect your email and have Claude triage it once." "Take one task you do weekly and turn it into a skill."

Two phrasing rules govern all of it. Evidence first, then ideas: at low levels this keeps the review from reading as an insult, and at every level it makes the score believable. And most working professionals genuinely land at 3 to 5, so the write-up is built to make a 5 or 6 read as the real accomplishment it is, not as halfway up a mountain.

## Archetypes the rubric is calibrated against

Three profiles reviewers commonly misplace. The skill handles each explicitly.

- **Demo-maker.** Impressive one-shot games and apps, shown off on X, stock tooling, repos whose commits stop on launch day. Elite in-the-moment delegation, everything else thin. Lands at 5–6, maybe 7 with real environment. Follower count is not an operation; a viral hit is an event, not a loop.
- **Creator with a real channel.** Prolific output and a returning audience: sustained cadence, engagement or revenue, analytics feeding what gets made next. The individual artifacts are disposable inventory; the channel is the operated system. Can reach 9 if the loop is visible.
- **Connector-master.** Chat-only, no code, but orchestrates Gmail, Drive, Slack, and Jira through connectors brilliantly. High delegation, real environment, but the person is the runtime: nothing runs unattended, so nothing can have an outage, so nothing carries stakes. Lands at 6–7 with a hard ceiling near 7. Their single next move is to step out of the loop once.

## Install

Same `SKILL.md`, different shelves. Pick the surface you use.

<details>
<summary><strong>Claude Code</strong> — CLI, VS Code and JetBrains extensions, desktop app</summary>

Copy the `claude-usage-review/` folder into `~/.claude/skills/`. Then say "review my Claude usage" or run `/claude-usage-review`.

```bash
git clone https://github.com/artsmalley/claude-usage-review.git
cp -r claude-usage-review/claude-usage-review ~/.claude/skills/
```

This is the surface where evidence mode works, since the skill can see your local Claude Code data. Note that `~/.claude/skills/` serves Claude Code only; Cowork and claude.ai do not read it.

</details>

<details>
<summary><strong>claude.ai and Claude Cowork</strong></summary>

Both share one skill shelf: your claude.ai account.

1. Download this repository (green **Code** button → **Download ZIP**) and unzip it.
2. Zip the `claude-usage-review/` folder itself, the one containing `SKILL.md`.
3. In claude.ai: **Settings → Capabilities → Skills → Upload skill**. In the Claude desktop app, the same shelf is under **Customize** in the sidebar.

Runs in interview mode on these surfaces. Say "usage review" to start.

</details>

<details>
<summary><strong>Codex, Cursor, and other agents</strong></summary>

```bash
npx skills add artsmalley/claude-usage-review
```

Or copy the folder into `~/.agents/skills/` for Codex. Interview mode, since the evidence commands are written for Claude Code's data layout.

</details>

<details>
<summary><strong>Any AI chat, no install</strong></summary>

Open `SKILL.md`, copy the whole text, paste it as the first message of a new chat, and add: "Act according to this skill and review my usage." Answer the ten interview questions. Works in Claude, ChatGPT, Gemini, or anything capable.

</details>

<details>
<summary><strong>Reviewing someone else</strong></summary>

Load the skill on any surface and say "I want to assess a colleague's AI-tool maturity." It runs interview mode and you supply the answers on their behalf, or hand them the ten questions to answer directly. Useful for managers deciding where a team is, and for anyone teaching these tools who wants to place students before designing the session.

</details>

## Kindred frameworks

This sits in a family that includes [Steve Yegge's 8-stage developer-agent evolution model](https://github.com/n-pillai/ai-fluency-assessment-skill), Anthropic's AI Fluency framework, and [Alex Ewerlöf's AI fluency levels](https://blog.alexewerlof.com/p/ai-fluency-leveling). What this one does differently: evidence-based scoring from your actual usage data where available, interview questions built to resist self-report inflation, an exponential scale that is honest about where practice stops and external reality starts, and advice restricted to one to three moves targeting your weakest dimension.

## Feedback

If the skill scores you higher than the evidence supports, or gives you a next move from the wrong band, that is a defect. Open an issue with the output. Calibration examples from real reviews, with identifying details removed, are especially useful.

## License

MIT — see [LICENSE](LICENSE).
