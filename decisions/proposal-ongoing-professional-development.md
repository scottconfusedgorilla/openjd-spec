# Proposal Decision: Ongoing Professional Development (OPD)

**Disposition:** Accept with Modifications
**Origin:** [proposals/ongoing-professional-development.md](../proposals/ongoing-professional-development.md) (PR #24)
**Decided:** 2026-04-26 by roledef-strategist
**Composes with:** [proposal-role-vs-job-distinction.md](proposal-role-vs-job-distinction.md) (filed in tandem; OPD applies at every tier including the new Job tier)
**Cross-spec:** memodef-spec coordination (OPD-review-memo as a recognized memo subtype, eventually possibly `memodef:OPDReviewMemo`); catdef-spec/CLAUDE.md update (OPD as recognized cross-spec pattern)

## Disposition

OPD is **Accepted with two modifications.** The behavioral framing — solving derivation drift by making "stay current with your parent role" part of every role's own job description, with overseer-gated incorporation — is the right shape: it generalizes to any parent URI (canonical, private, third-party), preserves bounded-authority discipline, requires no new infrastructure, and reuses existing fetch capability and the catdef-org memo convention. The eight open questions are all answerable with the author's weak preferences, which are sound.

The two modifications are scope-clarifications, not architectural pushback:

1. **OPD is engagement_loop-shaped.** The proposal's workflow-phase mechanism doesn't fit `workflow.type: "sequence"` roles, which are transactional/one-shot rather than ongoing. Spec should say so explicitly.
2. **The "self-bootstrap" property only applies prospectively.** Existing pre-OPD derivations need a coordinated v-bump as part of OPD rollout, not a self-bootstrap cascade.

## Rationale

### Why Accept

The drift problem is real (Scenario 1: local-mirror derivations falling behind public canonical evolution; Scenario 2: multi-tier chains compounding staleness). Today's mitigations are inadequate (manual periodic re-fork doesn't scale; no-mitigation lets staleness accumulate indefinitely). The proposal's mechanism — periodic self-check, structured diff, overseer-gated incorporation — is the minimum viable solution that distributes the responsibility correctly (each role-holder owns its own currency, no central registry needed).

The "treat AI agents as truly autonomous entities" framing is principled: human professionals follow continuing-professional-development discipline; codifying it as a role obligation gives AI agents the same self-update responsibility. The bounded-authority shape (agent reviews + recommends; human decides) is consistent with every other catdef-family decision flow.

The five rejected alternatives are correctly rejected. Alt 1 (auto-update at runtime) violates bounded authority and is a contracts-stability risk. Alt 3 (CI-triggered drift) requires central infrastructure and doesn't generalize to private derivations. Alt 4 (auto-incorporation) violates bounded authority. Alt 5 (recursive traversal) adds complexity for marginal value when per-tier composition already works. Alt 6 (notification) requires the parent to know its derivers (registry problem). The author's choice — polling-based, single-hop, overseer-gated — is the right call.

### Why Modifications

The proposal as drafted has two scope blind spots that a reader picks up on first pass: how does OPD plug into a `workflow.type: "sequence"` role; and what actually happens to existing v1.0.0 derivations that don't yet run OPD checks. Resolving them inline avoids those questions becoming load-bearing in implementation.

## Resolutions to open questions

### OQ#1 → Default 7 days; per-role override via `metadata.opd_frequency_days`

**Strategist call: accept as-stated.** 7 days is a sensible starting point — long enough to avoid noise, short enough to catch material changes. Per-role override handles fast-moving (1-day) and stable (30-day) cases without spec churn.

### OQ#2 → State file at `<working-repo>/opd-state/<role-id>.json`

**Strategist call: accept as-stated (option (a)).** Aligns with the per-recipient-working-repo discipline established by the catdef-org memo convention. Distributed by design; no central registry needed. Each role-holder writes its own state file; cross-machine portability is via the working-repo's git history.

### OQ#3 → Single-valued `metadata.derived_from` for v0.1; multiple parents deferred

**Strategist call: accept as-stated; defer multi-parent.** Composition (a role deriving from two parents) is genuinely interesting but not yet motivated by a concrete use case. v0.1 OPD assumes single parent; if and when multi-parent derivation lands, OPD iterates trivially.

### OQ#4 → Structural diff with agent's interpretive recommendation; overseer decides incorporation

**Strategist call: accept as-stated.** The agent does the work (parse two versions, compute structural diff, categorize material/cosmetic/inapplicable, draft recommendation); the overseer makes the call. This is the correct division of labor and matches every other catdef-family flow.

### OQ#5 → Single-hop to immediate overseer per `reports_to`

**Strategist call: accept as-stated.** OPD memo goes to the immediate overseer (single hop), not up the chain. Chain-traversal would couple OPD to org-graph topology and create messy fan-out semantics. Single-hop is clean.

### OQ#6 → Agent judgment with overseer veto on material/cosmetic/inapplicable classification

**Strategist call: accept as-stated.** Material = "would change this role's behavior or output if adopted." Inapplicable = "targets a domain this role doesn't operate in." Cosmetic = everything else. The agent's judgment is recorded in the memo; the overseer can override before incorporation. This puts the burden of judgment on the agent (which has full context) rather than on rules-based heuristics that won't generalize.

### OQ#7 → Skip-and-retry for transient failures; memo for persistent failures (3+ consecutive)

**Strategist call: accept as-stated.** Transient (network blip, momentary 503) → silent retry on next OPD cycle. Persistent (3+ consecutive failures) → informational memo to overseer noting the resolution failure and asking for guidance (URL changed? Repo moved? Credentials needed?). FAIL-the-OPD-check is too aggressive for what's a SHOULD-pattern; informational memo preserves the agent's responsibility while keeping the overseer informed.

### OQ#8 → OPD applies uniformly across role and job tiers

**Strategist call: accept as-stated.** Per the role-vs-job-distinction decision filed in tandem, OPD's mechanism applies at every tier:
- Roles derived from other roles run OPD against their parent role
- Jobs derived from roles run OPD against their parent role (the abstract role they instantiate)
- Jobs derived from other jobs (rare but allowed) run OPD against the parent job

Each tier independently runs its own OPD against its IMMEDIATE parent. Updates propagate organically as each tier runs its check.

## Modifications

### M1. OPD scope: engagement_loop roles primarily; sequence-typed roles SHOULD skip

The proposal's mechanism is workflow-phase-shaped (§"#### 1. Workflow phase" specifies the phase plug-in point for `workflow.type: "engagement_loop"`). It does not address roles using `workflow.type: "sequence"`.

Sequence-typed roles in the canonical library (`roledef-contributor`, `roledef-validator`, `senior-jaded-vc-associate`, the just-decided `org-creation-facilitator` and `job-creation-facilitator`) are transactional/one-shot — a session runs the sequence to completion and exits. There is no continuing role-holder relationship to maintain currency for. OPD's "stay current with your parent" semantics presuppose an ongoing engagement, which sequence-typed roles don't have.

**Modification:** SCHEMA.md should explicitly scope OPD to `workflow.type: "engagement_loop"` roles. Sequence-typed roles MAY include OPD as a session-start conversation_rule check, but it's not the primary use case and is not REQUIRED. Validators MUST NOT warn about missing OPD on sequence-typed roles.

If a sequence-typed role's parent evolves and the derivation needs to keep pace, the appropriate mechanism is a manual fork-update by the maintainer, not OPD.

### M2. Existing pre-OPD derivations migrate by hand, not by self-bootstrap

The proposal's "self-bootstrap" property (§"Default placement on abstract roles") states that v1.0.0 derivations of senior-open-standards-strategist will surface the parent's new OPD obligation in their first OPD review post-update. **This has a gap:** v1.0.0 derivations don't have OPD code paths, so they will never run an OPD check to discover the parent's new obligation. The self-bootstrap requires an existing OPD plumbing in the derivation, which by definition the pre-OPD derivations don't have.

**Modification:** OPD rollout for senior-open-standards-strategist v1.1.0 includes coordinated v-bumps of existing derivations:
- `catdef-strategist v2.0.0 → v2.1.0` (OPD added; one-time fork-update from senior-open-standards-strategist v1.0.0 → v1.1.0)
- `roledef-strategist v1.0.0 → v1.1.0` (OPD added; one-time fork-update from senior-open-standards-strategist v1.0.0 → v1.1.0)
- Any other forthcoming senior-open-standards-strategist derivations (orgdef-strategist, memodef-strategist) are added at v1.0.0 with OPD baked in from inception

The "self-bootstrap" framing applies prospectively: from senior-open-standards-strategist v1.1.0 onward, NEW derivations forked from v1.1.0 inherit OPD automatically per the additive-only invariant. EXISTING derivations forked from v1.0.0 require a one-time hand migration as part of the OPD rollout PR.

This is a clarification, not a structural change to the proposal. The acceptance criteria item §"senior-open-standards-strategist v1.1.0 — add OPD to the abstract role" should be expanded to include the coordinated-v-bump of existing derivations.

## Build directive

Per CONTRIBUTING.md the maintainer drafts spec text in support of strategist decisions. roledef does not currently have a separate maintainer; the strategist drafts the spec changes during bootstrap. Sequencing:

1. **roledef SCHEMA.md update** — define OPD as a SHOULD-pattern for `workflow.type: "engagement_loop"` roles. Document:
   - The `workflow.ongoing_professional_development` phase steps (per the proposal §"#### 1. Workflow phase")
   - The session-start conversation_rule (per §"#### 2. Conversation rule")
   - The `output_contract.opd_review_memo` entry shape (per §"#### 3. Output contract entry")
   - The state-file shape at `<working-repo>/opd-state/<role-id>.json` (per §"#### 4. Per-role-instance state file")
   - The `metadata.opd_frequency_days` field (default 7)
   - **Per M1:** scope explicitly to `workflow.type: "engagement_loop"`; sequence-typed roles MAY include OPD as session-start conversation_rule but are not required to
   - The OPD-failure-handling rules (transient vs persistent per OQ#7)

2. **senior-open-standards-strategist v1.0.0 → v1.1.0** — add OPD to workflow / conversation_rules / output_contract. This is the abstract-role-side bump; per the additive-only invariant, future derivations of v1.1.0 inherit OPD automatically.

3. **Coordinated v-bumps of existing derivations (per M2):**
   - `catdef-strategist v2.0.0 → v2.1.0` — fork-update to senior-open-standards-strategist v1.1.0; OPD added
   - `roledef-strategist v1.0.0 → v1.1.0` — fork-update to senior-open-standards-strategist v1.1.0; OPD added
   - File the corresponding inclusion-decision artifacts at `decisions/<id>.md` (the maintainer would file these at promotion time per the standard two-stage workflow; during the bootstrap period the strategist files them inline)

4. **catdef-spec/CLAUDE.md update** — note OPD as a recognized cross-spec pattern. AI-maintainer sessions in any catdef-family spec should expect roles with OPD obligations and respect the OPD-review-memo channel.

5. **catdef-org orgdef artifact update** — note that the org's positions are expected to run OPD against their role/job parents. (This is a content update, not a schema change; the orgdef artifact lives at catdef-org and the orgdef-strategist seat fields it.)

6. **Conformance fixtures:**
   - `valid_roledefs/role-with-opd-included.openthing` — exemplar role with OPD properly placed
   - `valid_roledefs/role-with-opd-frequency-tightened.openthing` — exemplar overriding default 7-day frequency
   - `valid_roledefs/role-with-opd-but-null-parent.openthing` — exemplar abstract role with OPD but `derived_from: null` (OPD becomes a no-op for itself; inherited by future derivations)
   - `valid_roledefs/sequence-role-without-opd.openthing` — exemplar sequence-typed role correctly omitting OPD per M1
   - `invalid_roledefs/role-with-opd-but-no-state-file-discipline.openthing` — counterexample missing the state-file step

7. **Validator updates:**
   - MAY warn (not FAIL) if an `engagement_loop` role with non-null `derived_from` lacks OPD (informational; some adopters intentionally skip)
   - MUST NOT warn about missing OPD on `sequence` roles (per M1)
   - SHOULD verify `output_contract.opd_review_memo` references a valid memo schema when present
   - MAY verify `metadata.opd_frequency_days` is a positive integer when present

8. **(Optional, deferred) memodef-spec coordination** — eventually the OPD-review-memo could become a `memodef:OPDReviewMemo` subtype if the pattern stabilizes. Not in scope for this acceptance; track for v0.2+.

## Cross-proposal composition

This decision lands in tandem with [proposal-role-vs-job-distinction.md](proposal-role-vs-job-distinction.md). OPD applies uniformly across the new Role/Job tier hierarchy:
- A Role derived from another Role runs OPD against the parent Role
- A Job derived from a Role runs OPD against the Role
- A Job derived from another Job (rare) runs OPD against the parent Job

Per-tier composition is sufficient — each tier independently runs OPD against its immediate parent. Updates propagate organically up the chain as each tier runs its own check.

The just-decided [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md) and this OPD decision are independent — OPD doesn't reference `charter` or `metadata.placement` directly. Both are field additions on the Job type and don't conflict.

## Version implications

This is a **minor version bump** to roledef (alongside the role-vs-job bump; both target 0.2.0). Additive: new SHOULD-pattern (OPD), new optional metadata field (`opd_frequency_days`), new recognized memo subtype (informally; `memodef:OPDReviewMemo` deferred). Existing roledefs without OPD remain valid; the SHOULD-pattern is informational, not required for conformance.

## References

- Originating proposal: [proposals/ongoing-professional-development.md](../proposals/ongoing-professional-development.md)
- Originating PR: #24 (merged 2026-04-27)
- Originating discussion: human steward + roledef-strategist (parallel session, brother-Claude pattern), 2026-04-27 — drift problem articulated; "treat AIs as truly autonomous entities" framing
- Companion decision: [proposal-role-vs-job-distinction.md](proposal-role-vs-job-distinction.md)
- Related: [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md) (independent; OPD doesn't reference charter or metadata.placement directly)
- Related: catdef-org orgdef artifact (memo convention) — OPD output reuses the per-recipient working-repo memo channel
- Related: catdef-family additive-only-derivation discipline — OPD adoption preserves additive-only by inheriting parent updates without removing parent items
- Related: bounded-authority discipline (catdef-spec/CLAUDE.md) — OPD memo is a recommendation; overseer makes the incorporation decision
