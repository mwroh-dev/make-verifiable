# make-verifiable

**Transforms vague requests into verifiable specs through a 7-phase HARD GATE protocol.**

Most clarification skills treat ambiguity as a single problem — ask more questions, dig deeper. This skill treats it as six distinct problems, each requiring a different methodology.

---

## The Problem

When you hand a vague request to an AI agent, the agent agrees. The issue is that nobody knows what the agent agreed to.

"Make it faster." "Clean this up." "Improve the user experience." These aren't requirements — they're invitations for the agent to hallucinate success criteria.

`make-verifiable` stops that moment before it starts.

---

## How It Works

Seven phases run sequentially. Each phase has a **HARD GATE** — a condition that must be satisfied before proceeding. Phases cannot be skipped based on subjective judgment ("this seems clear enough").

| Phase | Dimension | Methodology |
|-------|-----------|-------------|
| **0 — Context Gatherer** | Situational grounding | 5W1H / SCQA — without a concrete situation, all subsequent criteria are untethered |
| **1 — Refuter** | Falsifiability | Popper — a requirement with no refutation condition cannot be verified |
| **2 — Unanchorer** | Interpretation bias | Kahneman's anchoring effect — the first framing distorts all subsequent interpretation |
| **3 — Stratifier** | Verification classification | Testing pyramid — separates automated checks, judgment-dependent criteria, and human review |
| **4 — Floor-Setter** | Known unknowns | Rumsfeld's taxonomy — unverifiable items must be explicitly declared, not silently assumed |
| **5 — Drift Anchor** | Intent preservation | North Star principle — one sentence locks core intent and flags scope creep |
| **6 — Owner-Assigner** | Accountability | RACI — irreversible actions require an owner and a rollback condition |

Each phase attempts **Mode A** (auto-extract from context, confirm with user) first. If context is insufficient, it falls back to **Mode B** (targeted Socratic questions, one at a time).

Output: a `verifiable-spec.md` file usable as an implementation contract.

---

## When to Use

**Use when:**
- The request contains subjective criteria ("faster", "cleaner", "more intuitive")
- Scope boundaries are unclear
- The action is irreversible (deployments, data deletion, external sends)
- Multiple people could interpret the request differently
- You're delegating work to an AI agent

**Skip when:**
- The task is purely mechanical ("rename X to Y", "add import Z")
- A complete, testable spec already exists
- You're fixing a bug with clear, observable symptoms

---

## Installation

```bash
# curl
mkdir -p ~/.claude/skills/make-verifiable
curl -sL https://raw.githubusercontent.com/mwroh-dev/make-verifiable/main/SKILL.md \
  -o ~/.claude/skills/make-verifiable/SKILL.md

# git
git clone https://github.com/mwroh-dev/make-verifiable
cp make-verifiable/SKILL.md ~/.claude/skills/make-verifiable/SKILL.md
```

Invoke in Claude Code with `/make-verifiable`.

---

## Usage

```
/make-verifiable <request>
```

```
/make-verifiable 사용자 온보딩 플로우를 개선해줘
/make-verifiable 이 보고서를 더 설득력 있게 만들어줘
/make-verifiable 고객 데이터를 삭제하는 스크립트를 작성해줘
/make-verifiable API 응답 구조를 정리해줘
```

---

## Project Integration

```markdown
## Non-Negotiables
- [ ] Run `/make-verifiable` before starting any non-trivial task
- [ ] Do not begin execution without a verifiable-spec
```

---

## Files

```
make-verifiable/
├── SKILL.md           # skill definition
└── agents/
    └── openai.yaml    # Codex / OpenAI interface
```

---

## License

MIT
