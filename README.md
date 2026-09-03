# Claude Usage Review

A Claude skill that reviews how you actually use Claude (Claude Code, Cowork, or
claude.ai), scores you on a 1–10 maturity scale, and gives you 1–3 concrete next moves.
The advice is the product; the score is the hook.

## The scale

| Level | Anchor |
|---|---|
| **1–2 · Search substitute** | AI as a better Google. Ask, read, close the tab. |
| **3–4 · Copy-paste assistant** | Real drafts and snippets, carried to their destination by hand. Most working professionals are here. |
| **5–6 · Real delegator** | Hands over multi-step tasks end-to-end. Project context set up. Steers instead of redoing. |
| **7–8 · Environment builder** | Custom skills, hooks, MCP, automation. Claude sometimes works unattended. |
| **9–10 · Software producer** | AI is the primary production method. Ships software, agents, or a content operation others depend on. |
| **11 · Off-scale: field shaper** | Changes how *other people* use these tools. You don't self-assess an 11 — the field scores you. |

Scoring happens on four dimensions first — **delegation depth**, **environment
investment**, **autonomy granted**, **output shipped** — then rolls up by judgment,
not by average. The curve is exponential: 1–9 is learnable practice, 9→10 is a
distribution problem, 10→11 is a power-law outcome you can't will yourself into.

## Two modes

- **Evidence mode** (Claude Code): mines your local session transcripts, tool mix,
  skills/hooks/MCP setup, git co-authorship, and deploy configs. Objective — no
  self-report inflation. Aggregate stats only; it never reads message bodies.
- **Interview mode** (Cowork / claude.ai, or reviewing someone remotely): ten
  questions designed to resist grade inflation — including a toolchain check
  ("I built an app" with no git and no deploy target usually means an HTML file),
  a dependence test (does anyone use your work *repeatedly, without you*?), and a
  countermeasure ladder (a standing fix like a hook or CLAUDE.md rule scores higher
  than "I prompt better now").

## Install

**Claude Code (CLI, desktop, IDE):** copy the `claude-usage-review/` folder into
`~/.claude/skills/`, then say "review my Claude usage".

**claude.ai / Cowork:** zip the `claude-usage-review/` folder and upload it in
claude.ai Settings → Capabilities → Skills (the account skill shelf serves both
surfaces). Or, for a one-off, just attach `SKILL.md` to a conversation and answer
the interview questions.

## Kindred frameworks

This sits in a family that includes [Steve Yegge's 8-stage developer-agent evolution
model](https://github.com/n-pillai/ai-fluency-assessment-skill), Anthropic's AI Fluency
framework, and [Alex Ewerlöf's AI fluency levels](https://blog.alexewerlof.com/p/ai-fluency-leveling).
What this one does differently: evidence-based scoring from your actual usage data where
available, interview questions built to resist self-report inflation, and advice
restricted to 1–3 moves targeting your weakest dimension — never a generic tips list.

## License

MIT — see [LICENSE](LICENSE).
