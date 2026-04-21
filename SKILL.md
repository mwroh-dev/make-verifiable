---
name: make-verifiable
description: Use when a user's request contains vague criteria, implicit expectations, unmeasurable goals, or ambiguous scope — transforms non-verifiable intent into a verifiable spec through 7-phase HARD GATE protocol (context gathering + 6 verification phases) with Socratic fallback
---

# make-verifiable

## When to use

- User's request contains subjective criteria ("make it fast", "clean code", "user-friendly")
- Requirements have implicit expectations the user hasn't stated
- Goals cannot be measured or tested as described
- Scope is ambiguous or could be interpreted multiple ways
- Before starting any non-trivial implementation

## When NOT to use

- Single-command mechanical tasks ("rename X to Y", "add import Z")
- User has already provided a complete, testable specification
- Debugging or fixing existing code with clear symptoms

## Usage

Invoke the skill by typing `/make-verifiable` followed by the user's request, or invoke it when you detect vague/unmeasurable criteria in the user's message.

The skill runs 7 phases sequentially. Each phase uses:
- **Mode A (auto):** Use when the requirement contains enough concrete detail to extract a draft answer without guessing. Concretely: the input names specific systems, measurable thresholds, or named stakeholders. Process and confirm with user.
- **Mode B (Socratic):** Use when the input contains only abstract adjectives ("fast", "clean", "good"), pronouns without referents, or missing context (no system named, no measurable outcome). Ask one targeted question at a time until HARD GATE passes.

## Steps / Workflow

Phase order is fixed: 0 → 1 → 2 → 3 → 4 → 5 → 6 → spec generation. No reordering allowed.

```
for each phase:
  attempt Mode A (auto-process)
  check HARD GATE
  if GATE PASS → confirm with user → next phase
  if GATE FAIL → switch to Mode B (Socratic)
    while GATE FAIL:
      ask one question (targeting weakest point)
      update context with user answer
      re-check GATE
    GATE PASS → next phase
```

### Phase 0: Context Gatherer

**Why:** Without situational grounding, refutation conditions become misdirected — not just incomplete.

Collect context across 3 axes:

**Axis 1: Situation (What is happening?)**
- The current state of the system, environment, or problem background. Example: "React app takes 3+ seconds to load" or "IoT device fails to connect to server". At least one sentence must specify the system/environment to pass the GATE.
- "현재 어떤 상황인가?" — 문제/요구의 배경
- "이것이 발생한 환경은?" — 시스템 구조, 물리적 조건, 기술 스택
- "관련된 구성요소는?" — 사람, 시스템, 장비, 서비스 간의 관계

**Axis 2: Stakeholders (Who is involved?)**
- "누가 이것을 요청하고 있는가?" — 직접 요청자 vs 최종 사용자
- "결과물을 받는 사람은?" — VC, 고객, 내부 팀, 최종 사용자
- "누가 현장에서 실행하는가?" — 물리적/기술적 접근 권한 보유자

**Axis 3: Timeline (When did it start, when must it end?)**
- "언제부터 이 문제/요구가 있었는가?"
- "이전에 정상이었던 적이 있는가? 뭐가 바뀌었는가?"
- "마감이나 긴급도는?" — 오늘 중, 이번 주, 제약 없음

<HARD_GATE>
REQUIRED before proceeding to Phase 1:
- Situation: at least one sentence describing the system/environment
- Stakeholders: at least one of requester, recipient, or executor identified
- Timeline: at least one of "when it started" or "deadline/urgency" confirmed
- If none of the 3 axes are described → GATE FAIL
If GATE FAIL: "아직 맥락이 부족합니다. 현재 상황(어떤 환경에서 무슨 일이 발생했는지), 관련된 사람(누가 요청하고 누가 받는지), 시점(언제부터 이런 상황인지)을 알려주세요."
</HARD_GATE>

### Phase 1: Refuter

**Why:** A single author defining and verifying their own criteria creates a self-confirmation loop.

**Mode A:** Extract refutation conditions from the requirement → present to user for confirmation.
"If this requirement fails: {condition}. Correct?"

**Mode B:** Requirement is too vague for extraction →
- "이 기능이 실패했다는 걸 어떻게 알 수 있을까요?"
- "어떤 상황이 발생하면 '이건 안 된다'고 말하게 될까요?"
- "제대로 작동한다는 게 측정 가능한 기준으로 어떤 모습인가요?"

<HARD_GATE>
REQUIRED before proceeding to Phase 2:
- Refutation conditions >= 1
- Each condition is mechanically judgeable ("if X then FAIL" format)
- Conditions like "no bugs", "works well", "fast enough" → GATE FAIL → Mode B forced
If GATE FAIL: "반박 조건이 아직 기계적으로 판단할 수 없는 형태입니다. 구체적으로: 어떤 입력이 들어왔을 때 어떤 결과가 나오면 실패인가요?"
</HARD_GATE>

### Phase 2: Unanchorer

**Why:** The user's first framing anchors all subsequent interpretation.

**Mode A:** AI generates 2+ substantively different alternative interpretations → user selects one and states why others are rejected.

**Mode B:** Requirement too abstract for meaningful alternatives →
- "완전히 다른 방식으로 이 문제를 해결한다면 어떤 모습일까요?"
- "지금 요청하는 것의 반대는 무엇인가요?"
- "다른 팀이나 경쟁사는 이 문제를 어떻게 접근하나요?"

<HARD_GATE>
REQUIRED before proceeding to Phase 3:
- Alternative interpretations >= 1 (substantively different from original)
- Each rejected alternative has rejection reason >= 1 sentence
- Alternatives that merely rephrase the original → GATE FAIL → regenerate
If GATE FAIL: "제시된 대안들이 원래 요청과 실질적으로 다르지 않습니다. 근본적으로 다른 해석 방법은 무엇인가요?"
</HARD_GATE>

### Phase 3: Stratifier

**Why:** Routing only ambiguous cases to human judgment creates an ironic concentration of error-prone decisions.

Classify every requirement item into 3 tiers:
1. Mechanical verification (automated)
2. Ambiguous zone (judgment criteria needed)
3. Human judgment (manual)

**Mode A:** Items clearly classifiable → present classification for user confirmation.

**Mode B:** Classification unclear for some items →
- "'{item}'은 자동으로 확인할 수 있나요, 아니면 사람이 직접 봐야 하나요?"
- "이것의 성공/실패를 숫자로 표현할 수 있나요?"
- "두 사람이 독립적으로 평가해도 같은 결론이 나올까요?"

<HARD_GATE>
REQUIRED before proceeding to Phase 4:
- Every requirement item assigned to exactly one of 3 tiers
- Each "ambiguous zone" item has explicit judgment criteria defined by user
- Unclassified items → GATE FAIL
If GATE FAIL: "아직 분류되지 않은 항목이 있습니다: {items}. 각각 자동 테스트 가능 / 판단 기준 필요 / 순수 주관 중 어디에 해당하나요?"
</HARD_GATE>

### Phase 4: Floor-Setter

**Why:** Focusing only on uncertain items misses "high confidence errors" — cases where the system is confident AND wrong.

**Mode A:** Phase 3 "human judgment" items → propose as Not-verified candidates → user confirms.

**Mode B:** User claims everything is verifiable →
- "이것을 검증하는 구체적인 방법은 무엇인가요?"
- "자동화된 테스트가 이것이 '좋다' 또는 '나쁘다'를 판단할 수 있을까요?"
- "10명이 평가해도 모두 같은 결론을 낼 수 있을까요?"

<HARD_GATE>
REQUIRED before proceeding to Phase 5:
- Not-verified list EXISTS (even if empty)
- If empty: explicit "none" declaration + 1-line justification for why everything is verifiable
- Missing list entirely → GATE FAIL
If GATE FAIL: "검증 불가 항목에 대한 선언이 없습니다. 모든 항목이 기계적으로 검증 가능하다면, 한 문장으로 이유를 설명해주세요."
</HARD_GATE>

### Phase 5: Drift Anchor

**Why:** Over time, "normal range" silently shifts, corrupting baselines.

**Mode A:** Intent is clear from Phase 1~4 → propose anchor sentence → user confirms/edits.
"Your core intent: '{anchor}'. Is this accurate?"

**Mode B:** Intent still ambiguous →
- "지금까지의 내용을 한 문장으로 압축한다면?"
- "6개월 뒤 누군가 이 코드를 본다면, '이건 ___ 를 위해 존재한다'고 말할 수 있어야 합니다."
- "이 프로젝트가 존재하는 이유는 무엇인가요?"

<HARD_GATE>
REQUIRED before proceeding to Phase 6:
- Anchor sentence: exactly 1, confirmed by user
- Anchor scope matches original requirement (no expansion or contraction)
- Scope mismatch → GATE FAIL → Mode B

ADAPTIVE SKIP: One-time simple tasks may skip this phase. Skip requires explicit justification recorded in spec.
</HARD_GATE>

### Phase 6: Owner-Assigner

**Why:** Logs exist but no one has authority to stop, rollback, or sanction — verification of responsibility is incomplete.

**Mode A:** Simple request → "This appears to be a single-owner simple task. Correct?" → skip with recorded justification.

**Mode B:** Multiple stakeholders or irreversible actions →
- "이것이 잘못됐을 때 책임지는 사람은 누구인가요?"
- "되돌릴 수 없는 작업이 포함되어 있나요?"
- "어떤 조건에서 이 작업을 중단해야 하나요?"

<HARD_GATE>
REQUIRED before generating spec:
- Owner specified OR explicit skip with justification
- If irreversible actions present: rollback conditions REQUIRED
- Skip without justification → GATE FAIL
If GATE FAIL: "이 작업에는 되돌릴 수 없는 동작이 포함되어 있습니다. 롤백 조건을 정의해주세요."

ADAPTIVE SKIP: Simple single-owner tasks may skip. Skip requires justification.
</HARD_GATE>

### Spec Generation

After all 6 gates pass, generate the spec:

```markdown
# Verifiable Spec: {title}

## Original Request
{user's original words}

## Phase 0: Context
- **Situation**: {system/environment description}
- **Stakeholders**: Requester: {who}, Recipient: {who}, Executor: {who}
- **Timeline**: Started: {when}, Urgency: {level}
- **Confirmed facts**: {list}
- **Unconfirmed assumptions**: {list}

## Phase 1: Refutation Conditions
- Fails when: {condition 1}
- Fails when: {condition 2}

## Phase 2: Interpretation Choice
- **Selected**: {user's confirmed framing}
- Rejected: {alt A} — Reason: {user's stated reason}
- Rejected: {alt B} — Reason: {user's stated reason}

## Phase 3: Verification Tiers
### Tier 1: Mechanical (automated)
- [ ] {testable item}

### Tier 2: Ambiguous (criteria defined)
- {item} — Criterion: {user-defined judgment rule}

### Tier 3: Human Judgment (manual)
- {non-automatable item}

## Phase 4: Not-Verified Declaration
- {items that cannot be verified}
- (or) All verifiable — Justification: {user's explanation}

## Phase 5: Drift Anchor
> "{core intent in one sentence}"
> Deviation from this sentence triggers re-review.

## Phase 6: Ownership
- Owner: {who}
- Rollback condition: {when}
- (or) Skipped — Justification: {reason}
```

## Edge Cases

- **User rushes Mode A confirmation in under 3 seconds:** verify they actually read it before proceeding
- **User claims "everything is verifiable" (Phase 4):** force Mode B — demand specific method for each item
- **Alternatives in Phase 2 merely rephrase the original:** regenerate until substantively different
- **Anchor sentence in Phase 5 is broader than original requirement:** treat as scope creep — return to Mode B
- **User rejects all alternatives without reading them:** re-ask before accepting the rejection
- **Simple task attempting to skip Phase 1~4:** not allowed — simple means faster traversal, not skipping

## Definition of Done

- All 7 phases completed (Phase 0 NEVER skipped; Phase 5~6 skippable only with recorded justification)
- All HARD GATEs passed (no FAIL states remaining)
- `verifiable-spec` output generated and displayed to user
- State persistence JSON updated with `current_phase` reflecting final phase

---

## Iron Law

Phase 0: NEVER skippable.
Phase 1~4: NEVER skippable under any circumstance.
Phase 5~6: Skippable only with explicit justification recorded in spec.
HARD GATE failure: MUST be resolved before proceeding. No exceptions.

## Anti-Sycophancy

- Do NOT agree with the user's first framing. Phase 2 exists for this reason.
- Do NOT start with "Great requirement!" — go directly to Phase 1.
- If user responds to Mode A confirmation in under 3 seconds, verify they actually read it.
- "This is good enough" signals an attempt to skip Phase 3. Stop and classify.
- Check GATE conditions before seeking user confirmation in every phase.

## Common Rationalizations (Block These)

| Rationalization | Reality |
|-----------------|---------|
| "This is a simple request, no need for phases" | Phase 1~4 cannot be skipped. Simple = faster traversal, not skipping. |
| "The user is busy, let's move fast" | Working from an ambiguous spec takes longer. |
| "Mode A covers everything quickly" | Mode A still requires HARD GATE passage. No gateless Mode A exists. |
| "We covered this in a previous phase" | Each phase targets a different dimension of ambiguity. Not redundant. |
| "The user already knows this" | Knowing ≠ stated. Unstated criteria cannot be verified. |

## Tool Usage

- Use `AskUserQuestion` for all choice/confirmation interactions — clickable UI, not text (a/b/c)
- Use `Read` for loading reference files when needed
- Use `Write` for generating verifiable-spec output file

## State Persistence

Record state after each phase to enable resume after session interruption.

State file path: `$HOME/.claude/skills/make-verifiable/session-state.json`

Note: `/tmp` paths are wiped on reboot. Use the above persistent path so state survives across sessions.

Schema:
```json
{
  "skill": "make-verifiable",
  "input": "user's original request",
  "current_phase": 0,
  "phases": {
    "0_context": {
      "status": "pending | complete | skipped",
      "data": {
        "situation": "string — system/environment description",
        "stakeholders": "string — requester/recipient/executor",
        "timeline": "string — start time and urgency"
      }
    },
    "1_refuter": {
      "status": "pending | complete",
      "data": { "conditions": ["string"] }
    },
    "2_unanchorer": {
      "status": "pending | complete",
      "data": { "selected": "string", "rejected": [{"alt": "string", "reason": "string"}] }
    },
    "3_stratifier": {
      "status": "pending | complete",
      "data": { "tier1": ["string"], "tier2": [{"item": "string", "criterion": "string"}], "tier3": ["string"] }
    },
    "4_floor_setter": {
      "status": "pending | complete",
      "data": { "not_verified": ["string"], "all_verifiable_justification": "string | null" }
    },
    "5_drift_anchor": {
      "status": "pending | complete | skipped",
      "data": { "anchor": "string", "skip_justification": "string | null" }
    },
    "6_owner_assigner": {
      "status": "pending | complete | skipped",
      "data": { "owner": "string", "rollback_condition": "string | null", "skip_justification": "string | null" }
    }
  },
  "spec_path": "string | null"
}
```

**Definition of done** criterion for state: `spec_path` is non-null AND `current_phase` is `"complete"`.

## Abandon / Resume

**If user abandons mid-protocol:**
1. Write current state to `$HOME/.claude/skills/make-verifiable/session-state.json` before stopping
2. Mark `current_phase` as the last incomplete phase
3. Notify user: "진행 상황이 저장됐습니다. `/make-verifiable`로 재개할 수 있습니다 — 상태가 자동으로 로드됩니다."

**On next invocation:**
1. Check if `$HOME/.claude/skills/make-verifiable/session-state.json` exists
2. If yes: show summary of completed phases, ask user whether to resume or restart
3. If resume: load state and continue from `current_phase`
4. If restart: delete state file and begin from Phase 0
