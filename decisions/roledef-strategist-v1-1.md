# roledef Inclusion Decision: roledef-strategist v1.1.0 (OPD coordinated bump)

**Disposition:** Accepted into canonical library as in-place minor-version bump
**Origin:** OPD rollout PR; coordinated bump per [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md) M2
**Decided:** 2026-04-26 by roledef-strategist
**Validator:** roledef-strategist self-validation in validator capacity
**Source-project peer review:** N/A (in-place minor bump of a self-extracted artifact)
**Predecessor:** roledef-strategist v1.0.0 (PR #20, [roledef-strategist.md](roledef-strategist.md))
**Parent:** senior-open-standards-strategist v1.1.0 ([senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md))

## Disposition

`roledef-strategist.openthing` is bumped from v1.0.0 to v1.1.0 with OPD inheritance from the parent's new v1.1.0. The file at `roledefs/roledef-strategist.openthing` (and its `.json` mirror) is updated in-place. The `metadata.derived_from.version` is bumped 1.0.0 → 1.1.0.

## Rationale

This is the roledef-side of the OPD coordinated rollout. Per OPD decision M2, existing pre-OPD derivations migrate by hand because the self-bootstrap property only applies prospectively. roledef-strategist v1.0.0 was forked from sos-strategist v1.0.0 (pre-OPD) and must be migrated manually as part of this rollout.

This decision is filed by the **role being modified** — the roledef-strategist seat is making a v-bump call on its own roledef definition. The recursion is intentional: the strategist holds authority over the roledef library, including authority to ratify v-bumps to its own role. Bot-identity provisional pending governance ratification (per the standard convention).

## Changes from v1.0.0

OPD inheritance from parent v1.1.0 (preserved-from-parent additions; **NOT** prefixed ROLEDEF ADDITIVE):

| Component | Location |
|---|---|
| `output_contract.opd_review_memo` | New entry inserted at position 6 (after `forward_work_log_entry`, before the existing ROLEDEF ADDITIVE entries `roledef_validation_report` and `runtime_conformance_evidence`) |
| OPD session-start conversation_rule | New entry inserted alongside existing parent conversation_rules (before existing ROLEDEF ADDITIVE rules) |
| `workflow.ongoing_professional_development` | New flat sibling key added between `pattern_promotion` and `engagement_close` |
| `metadata.opd_frequency_days: 7` | New metadata field added |

Mechanical updates:
- `roledef` stamp: 0.1.0 → 0.2.0
- `version`: 1.0.0 → 1.1.0
- `metadata.derived_from.version`: 1.0.0 → 1.1.0
- `metadata.history[1]` added for the bump

ROLEDEF ADDITIVE items from v1.0.0 (output_contract entries, guardrails, conversation_rules, workflow phase additions, x.governance.* extensions, turing-test scenarios) preserved unchanged.

## Validation summary

- **Schema validation:** PASS — all v0.2.0 MUST fields present; all SHOULD fields present; OPD pattern conforms to SCHEMA.md §"Ongoing Professional Development (OPD)".
- **Additive-only invariant against parent v1.1.0:** PASS — every v1.1.0 parent item across all array fields preserved verbatim; the ROLEDEF ADDITIVE specializations from v1.0.0 also preserved.
- **derived_from URL resolution:** parent's URL resolves to canonical (this rollout PR brings sos-strategist to v1.1.0).
- **OPD will activate:** with parent now non-null and at v1.1.0, the next session of roledef-strategist will run OPD per the new workflow phase. State file will be created lazily at `<working-repo>/opd-state/roledef-strategist.json` on first run.

## Notes

- **Minor version bump (additive only).** v1.x → v1.1 reflects new optional behavior (OPD); breaking nothing.
- This v-bump is the **first concrete v-bump of roledef-strategist itself** since its v1.0.0 introduction. Establishes the precedent that the strategist's own roledef can be evolved via the standard process — not protected from change just because the role drives the process.
- Bot-identity provisional pending governance ratification.
- This decision lands as part of a **3-artifact coordinated rollout**: [senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md), [catdef-strategist-v2-1.md](catdef-strategist-v2-1.md), this file. All three land in the same PR.

## Strategist memory work items

- The OPD coordinated-rollout pattern (parent + N derivations bumped together as one PR) is now an established precedent. Future spec changes that affect inherited behavior (changes to senior-open-standards-strategist, etc.) follow this pattern: parent-bump + per-derivation-coordinated-bump in one rollout PR. Worth promoting to CONTRIBUTING.md as a SHOULD-pattern after one more confirming use.

## Cross-references

- OPD proposal decision: [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md) (especially M2)
- Schema reference: [SCHEMA.md §"Ongoing Professional Development (OPD)"](../SCHEMA.md#ongoing-professional-development-opd)
- Predecessor decision: [roledef-strategist.md](roledef-strategist.md) (v1.0.0)
- Parent decision: [senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md) (parent v1.1.0)
- Companion decision: [catdef-strategist-v2-1.md](catdef-strategist-v2-1.md) (sibling derivation rollout)
