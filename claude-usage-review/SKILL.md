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

# Shipped-code signals (code vs documents-only; real tools vs one-shot artifacts)
find ~/projects ~/code ~/dev -maxdepth 2 -name ".git" -type d 2>/dev/null | wc -l   # real repos
find ~/projects -maxdepth 2 \( -name "vercel.json" -o -name "Dockerfile" -o -name "fly.toml" -o -name "railway.json" \) 2>/dev/null  # deploy configs
git -C <their-main-repo> log --oneline --grep="Co-Authored-By: Claude" | wc -l       # Claude-coauthored commits
```

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
   connected tools (email, calendar, files)?
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
- **Output shipped** — has the work left the machine? Published articles, deployed apps,
  running bots, tools other people use.

Roll-up anchors (observable behavior, not vibes):

| Level | Anchor |
|---|---|
| **1–2 · Search substitute** | Asks questions, reads answers. Nothing persists between sessions. AI = better Google. |
| **3–4 · Copy-paste assistant** | Gets drafts, code snippets, emails; carries them to their destination by hand. Redoes rather than steers when output is wrong. |
| **5–6 · Real delegator** | Hands over multi-step tasks end-to-end. Has project context set up. Iterates with the agent. Connected at least one real system (files, email, repo). |
| **7–8 · Environment builder** | Custom skills/hooks/MCP/automation. Subagents or scheduled runs. Claude sometimes works unattended. Their setup would take a newcomer weeks to replicate. |
| **9–10 · Software producer** | AI is the primary production method. Ships software, agents, or a content operation built and run this way. Orchestrates multiple agents/sessions routinely. |
| **11 · Off-scale: field shaper** | Not "uses it better" — changes how *other people* use it. Publishes methods others adopt, ships agent products or tooling with real adoption, or their internal practices spread through an organization or community. You don't score an 11 by self-assessment; the field scores you. Judge by adoption of their methods, not follower count — some 11s are publicly prominent, others are invisible platform builders inside companies. Canonical example: Andrej Karpathy. |

The curve is exponential, with two step changes:
- **1–9 is learnable practice.** Each level is behavior anyone can adopt by deciding to;
  you do not need to be a professional software engineer — agents supply the SDE work,
  the user supplies delegation judgment and persistence.
- **9→10 is a distribution problem**, not a skill problem: strangers must depend on what
  you shipped, which means finding users and doing the operational reliability work.
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

## Output format

Keep it short — one screen:

1. **Score X/10** with a one-line characterization in plain words.
2. **Dimension profile** — the four dimensions, each with score and ONE line of evidence.
3. **What's working** — 1–2 lines. Genuine, specific, no flattery.
4. **Next moves** — exactly 1–3, targeting the weakest dimension, each concrete enough to
   do this week ("write a CLAUDE.md for your main project", "connect your email and have
   Claude triage it once", "take one task you do weekly and turn it into a skill").
   Never a generic tips list.

Do not pad. Do not include the rubric table in the output. If evidence mode found little
data, say so and lower confidence rather than inventing a score.
