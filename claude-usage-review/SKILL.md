---
name: claude-usage-review
description: >
  Review a person's usage of Claude Code / Claude Cowork / claude.ai, score it 1-10
  (with an off-scale 11), and give 1-3 concrete next moves. Use when the user says
  "usage review", "review my Claude usage", "score my Claude usage", "how am I using
  Claude", or wants to assess someone else's AI-tool maturity.
---

# Claude Usage Review

Assess how someone actually uses Claude (and AI coding tools generally), place them on a
1–10 maturity scale, and give them the few next moves that would raise their level.
The advice is the product; the score is the hook.

## Two modes — pick by what data exists

**Evidence mode** — the person runs Claude Code and you are on their machine. Mine local
data; do not rely on self-report. Use this whenever `~/.claude/projects/` exists.

**Interview mode** — Cowork or claude.ai only, or reviewing someone remotely. No local
data; ask the interview questions below and score from answers. Say clearly in the output
that the score is self-report-based.

If both apply (Code locally + Cowork in browser), run evidence mode and add 2–3 interview
questions about the surfaces you can't see.

## Evidence mode: what to mine

Aggregate stats only — NEVER read transcript message bodies (privacy, and they're huge).
All commands below are read-only.

```bash
# Scale and tenure
ls ~/.claude/projects | wc -l                                  # distinct projects
find ~/.claude/projects -name "*.jsonl" | wc -l                # sessions
find ~/.claude/projects -name "*.jsonl" -printf '%T@\n' | sort -n | sed -n '1p;$p'  # first/last activity

# Tool mix over recent work (sample ~150 recent transcripts; full corpus is too slow)
find ~/.claude/projects -name "*.jsonl" -printf '%T@ %p\n' | sort -rn | head -150 | cut -d' ' -f2- | \
  xargs cat | jq -r 'select(.type=="assistant") | .message.content[]? | select(.type=="tool_use") | .name' 2>/dev/null | \
  sort | uniq -c | sort -rn

# Environment investment
ls ~/.claude/skills/ 2>/dev/null                               # personal skills
jq -r '.hooks | keys[]' ~/.claude/settings.json 2>/dev/null    # hooks
jq -r '[.projects[]?.mcpServers // {} | keys[]] | unique[]' ~/.claude.json 2>/dev/null  # MCP servers
find ~ -maxdepth 4 -name "CLAUDE.md" 2>/dev/null | head -20    # project context files
crontab -l 2>/dev/null; systemctl --user list-timers 2>/dev/null | head  # automation

# Customization inventory (has the person modified the tool itself?)
jq 'keys' ~/.claude/settings.json 2>/dev/null                  # what's configured at all
jq '.permissions | {allow: (.allow|length), ask: (.ask|length), deny: (.deny|length)}' \
  ~/.claude/settings.json 2>/dev/null                          # tuned permission lists
ls ~/.claude/commands/ ~/.claude/agents/ ~/.claude/output-styles/ 2>/dev/null  # commands/agents/styles
ls ~/.claude/keybindings.json ~/.claude/plugins 2>/dev/null    # keybindings, plugins
find ~ -maxdepth 4 \( -path "*/.claude/settings.json" -o -path "*/.claude/settings.local.json" \
  -o -path "*/.claude/commands" -o -name ".mcp.json" \) 2>/dev/null | head -20  # per-project tuning
ls ~/.claude/projects/*/memory/MEMORY.md 2>/dev/null | head    # auto-memory in use
```

Interpret the inventory: a fat, curated permissions allow/ask list is one of the strongest
"modifies the tool" signals — it means the person hit friction and engineered it away.
Per-project settings and commands beat a single global config (they tune per context).
Stock settings.json with only defaults = the tool was installed, not adopted.

```bash
# Modification over time (practice vs possession)
find ~/.claude/skills ~/.claude/commands ~/.claude/agents -name "*.md" \
  -printf '%TY-%Tm-%Td %s %p\n' 2>/dev/null | sort              # mtime + size per artifact
find ~ -maxdepth 4 -name "CLAUDE.md" -printf '%TY-%Tm-%Td %p\n' 2>/dev/null | sort | head -20
stat -c '%y %n' ~/.claude/settings.json ~/.claude/keybindings.json 2>/dev/null
# If a config/skill lives in a git repo, history is the best evidence — iteration count + cadence:
git -C <repo> log --oneline --follow -- CLAUDE.md .claude/ | head -20

# Shipped-code signals (code vs documents-only; real tools vs one-shot artifacts)
find ~/projects ~/code ~/dev -maxdepth 2 -name ".git" -type d 2>/dev/null | wc -l   # real repos
find ~/projects -maxdepth 2 \( -name "vercel.json" -o -name "Dockerfile" -o -name "fly.toml" -o -name "railway.json" \) 2>/dev/null  # deploy configs
git -C <their-main-repo> log --oneline --grep="Co-Authored-By: Claude" | wc -l       # Claude-coauthored commits
```

Interpret modification over time: this is the discriminator for the 7–8 band. Environment
building is a practice, not an inventory — mtimes and git history observe practice.
A CLAUDE.md or skill revised across weeks beats ten stubs created in one sitting; clustered
identical mtimes = one setup afternoon (often from a blog post), spread mtimes = ongoing
improvement cycles. Recent mtimes on config files mean the tuning is alive, not abandoned.

Interpret the tool mix: heavy `Edit`/`Write`/`Bash` = real delegation; `Task`/`Agent` =
subagent orchestration; `Skill` = uses packaged workflows; `mcp__*` = integrated external
systems; almost nothing but text turns = chat-only usage. Also ask the person what they've
**shipped** with it — deployed sites, running agents/bots, published work — since that
never shows up in transcripts directly.

## Interview mode: the questions

1. Describe the last three things you asked Claude to do, concretely.
2. When Claude gives you something, what happens next — do you paste it somewhere by hand,
   or does Claude put it where it belongs itself?
3. Do you have any standing setup — project instructions, a CLAUDE.md, custom skills,
   connected tools (email, calendar, files, Slack, Jira, Asana)?
4. Has Claude ever done a multi-step task end-to-end for you (research → write → file/send)?
5. Has Claude ever worked while you weren't watching — scheduled, background, overnight?
6. What comes out of your Claude work — documents and analyses, working code, or running
   services? (All are legitimate; the mix places the person.)
7. Have you built a real tool with it — something that runs *outside* the chat window,
   takes real inputs, and would still work tomorrow with Claude turned off? A one-shot
   artifact or demo HTML file doesn't count here.
8. Do you use version control (git/GitHub) or any deploy target (Vercel, a server, an app
   store, a bot host)? Where does your stuff actually live and run?
9. When Claude gets something wrong repeatedly — the same kind of mistake more than once —
   what have you actually done about it? Give a real example of the last time.
   The answer ladder: gave up (1–2) → redid it by hand (3–4) → steered with a better
   prompt in the moment (5–6) → changed the system so that error class can't recur:
   a CLAUDE.md rule, a hook, a permission setting, a skill, a checklist the agent runs
   (7+). "I reflect and prompt better now" without a concrete standing fix scores as
   steering, not as a system change.
10. Does anyone use something you made *repeatedly, without you involved* — and would
    they notice if it broke tomorrow? Who, and how often? (Sending a file to a colleague
    once doesn't count; a page people return to, a bot they message, a tool in someone's
    routine does.)

Question 8 is a lie detector for inflated answers to 6–7: "I built an app" with no git,
no repo, and no deploy target usually means "Claude made me an HTML file." But the
toolchain is a PROXY for shipping, not the thing itself — and verified dependence (Q10)
is the thing itself, so it overrides. Someone who built a tool dozens of colleagues now
rely on, and had IT put it on a company server, shipped for real: in a large company,
IT-as-deployment-department is a normal route, not a gap. Score their output-shipped on
the dependence evidence and note the toolchain as a growth edge, not a cap. The cap
(output-shipped ~5–6) applies only when BOTH are absent: no toolchain AND no verifiable
"who uses it, how often" answer.
Question 10 uses the same defense as 7: recurrence and dependence, not a one-time handoff.
"Yes" without a who/how-often answer gets discounted; "yes" WITH names, frequency, and a
tool that would be missed tomorrow counts fully — regardless of who runs the server.

## The scale

Score on FOUR dimensions first (each 1–10), then roll up by judgment — not an average.
A person is usually best described by their profile ("delegates deeply but has built no
environment") rather than the roll-up alone.

- **Delegation depth** — from asking questions → getting drafts → handing over whole
  multi-step tasks with acceptance criteria.
- **Environment investment** — CLAUDE.md files, skills, hooks, MCP connections, permission
  tuning, memory. How much have they built so Claude works better *for them specifically*?
- **Autonomy granted** — does Claude only act while watched, or run background tasks,
  scheduled jobs, agents that operate unattended?
- **Output shipped** — has the work left the machine, and does anything *depend* on it?
  Weighted by stakes and post-launch iteration, not artifact count: a deploy config and a
  live URL are possession signals; a repo whose feature commits continue for months after
  launch is an operation. Claude writing into shared team systems — posting the Slack
  summary, updating Jira tickets others act on — counts as intermediate shipping: more
  than on-machine, less than an operated product. For enterprise users who can't deploy,
  it's the main road from 6 toward 8.

Roll-up anchors (observable behavior, not vibes):

| Level | Anchor |
|---|---|
| **1–2 · Search substitute** | Asks questions, reads answers. Nothing persists between sessions. AI = better Google. |
| **3–4 · Copy-paste assistant** | Gets drafts, code snippets, emails; carries them to their destination by hand. Redoes rather than steers when output is wrong. |
| **5–6 · Real delegator** | Hands over multi-step tasks end-to-end. Has project context set up. Iterates with the agent. Connected at least one real system — files, email, repo, or a work tool (Slack, Jira, Asana, Teams, Notion). |
| **7–8 · Environment builder** | Custom skills/hooks/MCP/automation. Subagents or scheduled runs. Claude sometimes works unattended. Their setup would take a newcomer weeks to replicate. An 8 also has at least one thing off the machine that someone else has actually used; a purely self-serving environment, however deep, stays 7. |
| **9 · Operator** | Runs something with real stakes — external users, a production workload, or a paying/returning audience — with a live agentic improvement loop: feedback or usage data flows in, agent-built updates ship on a visible cadence. Someone or something would be hurt by an outage. |
| **10 · Production at scale** | Multiple such operations, or one that strangers depend on routinely. Reliability work (monitoring, recovery) exists because it has to. AI is the production method of a real operation, not a portfolio. |
| **11 · Off-scale: field shaper** | Not "uses it better" — changes how *other people* use it. Publishes methods others adopt, ships agent products or tooling with real adoption, or their internal practices spread through an organization or community. You don't score an 11 by self-assessment; the field scores you. Judge by adoption of their methods, not follower count — some 11s are publicly prominent, others are invisible platform builders inside companies. Canonical example: Andrej Karpathy. |

**Stakes, not headcount.** "Users" is a proxy. The 9 gate is an operated system where
FAILURE HAS REAL COST, in any medium:
- *Software*: external users — internal/company users count fully (see Q8/Q10 defense).
  Frequency beats headcount: 12 daily users who'd notice an outage by lunch outrank
  500 registered accounts that log in quarterly. A standing agent living in a work tool
  qualifies — a Slack bot teammates message, a triage agent working the Jira queue the
  team would miss by lunch.
- *Infrastructure*: a production workload with zero human users still qualifies — a backup
  daemon guarding irreplaceable data, a pipeline moving money. Shrug test: a failure you'd
  shrug at and rerun is convenience; one you'd feel for weeks is stakes.
- *Creators / community*: an audience, membership, or revenue stream the pipeline sustains —
  a channel with a cadence sponsors pay for, a paid community, a newsletter with real
  readership. No engineering skill required; the loop evidence just looks different:
  publishing cadence held by agents, analytics feeding content decisions, audience
  feedback visibly changing the product.

In every medium the LOOP is the discriminator between 8 and 9: merely using AI to make
things — however sophisticated — is 8; operating an AI-run system with feedback flowing
back into agent-built updates is 9.

The curve is exponential, with two step changes:
- **1–8 is learnable practice.** Each level is behavior anyone can adopt by deciding to;
  you do not need to be a professional software engineer — agents supply the SDE work,
  the user supplies delegation judgment and persistence.
- **8→9 is where external reality enters**: something with stakes must exist and be
  operated, which means finding users (or carrying a real workload) and closing the loop.
  9→10 is then depth of dependence and scale, not first contact with the world.
- **10→11 is a power-law outcome** the person doesn't control — the field must adopt
  their methods. You can only do 11-quality public work and let selection operate.

Advice must stay inside the controllable range: next moves target the weakest dimension,
and no dimension is "be famous." Never advise someone to aim at 11.

Calibration rules:
- Most working professionals genuinely land at 3–5. A 5–6 is a real accomplishment —
  write the output so it reads that way, not as "halfway up the mountain."
- Judgment over box-ticking: someone can hit 7 with zero MCP servers if their delegation
  and autonomy are deep. The anchors are vantage points, not a checklist.
- Score what they DO, not what they know. Knowing about subagents ≠ using them.
- Evidence beats claims. In interview mode, discount vague answers ("I use it for
  everything") that lack a concrete example.

## Feedback ladder

Two phrasing rules for ALL feedback:
1. **Evidence first, then ideas.** State what the person is observably doing, as fact —
   not praise, not deficit. Then offer ideas to try. Evidence makes the score believable;
   at low levels it keeps the review from reading as an insult.
2. **Next moves come from ONE band up, never the summit.** A level-2 person told to
   "build a skill" bounces off; a level-8 person already did it. The right idea is always
   the behavior of the next band.

Calibration examples (evidence → ideas), one per major band:

**Level 3** — *Evidence:* gets real work product out several times a week, but everything
makes its final trip by hand — copied from chat into the doc, the email, the file; fixes
wrong output personally rather than sending it back. *Ideas:* have Claude write the next
document directly where it lives and revise it there; when output is wrong, say what's
wrong and make Claude fix it — one steer before taking over; keep the most common type of
ask in one project so context stops resetting.

**Level 5** — *Evidence:* hands over whole multi-step tasks and iterates to done; project
context exists; at least one real system connected; corrections live in the moment and get
retyped across sessions. *Ideas:* any correction typed twice becomes a CLAUDE.md rule this
week; package one weekly task as a skill; first unattended run — hand over a task and
judge the result, not the process.

**Level 7** — *Evidence:* custom skills, tuned permissions, some automation; Claude
sometimes works unwatched; the setup would take a newcomer weeks — but nearly everything
produced still ends on their own machine. *Ideas:* error-proof systematically — turn the
last twice-made mistake into a hook or rule so the class can't recur; split one big job
across parallel subagents; ship one thing to where someone else can touch it.

**Level 8, aiming at 9** — *Evidence:* deep environment, real deployments or a real
publishing pipeline, distribution started — but no closed loop: usage isn't measured,
feedback doesn't visibly change the product, nothing would be missed by lunch. *Ideas:*
pick ONE deployed thing and get a handful of real users (or one real workload) on it;
open a feedback channel and ship one agent-built update in response to it; add the
monitoring/recovery the stakes now demand. The next moves ARE the 9 criteria.

## Output format

Keep it short — one screen:

1. **Score X/10** with a one-line characterization in plain words.
2. **Dimension profile** — the four dimensions, each with score and ONE line of evidence.
3. **What the evidence shows** — 1–2 lines of what they're observably doing, phrased as
   fact. Specific, no flattery, no deficit-framing.
4. **Next moves** — exactly 1–3, targeting the weakest dimension, each concrete enough to
   do this week ("write a CLAUDE.md for your main project", "connect your email and have
   Claude triage it once", "take one task you do weekly and turn it into a skill").
   Never a generic tips list.

Do not pad. Do not include the rubric table in the output. If evidence mode found little
data, say so and lower confidence rather than inventing a score.
