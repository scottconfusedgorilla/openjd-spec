# roledef Inclusion Decision: catdef-strategist v2.1.0 (OPD coordinated bump)

**Disposition:** Accepted into canonical library as in-place minor-version bump
**Origin:** OPD rollout PR; coordinated bump per [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md) M2
**Decided:** 2026-04-26 by roledef-strategist
**Validator:** roledef-strategist self-validation in validator capacity
**Source-project peer review:** N/A (in-place minor bump of a self-extracted artifact)
**Predecessor:** catdef-strategist v2.0.0 (PR #19, [catdef-strategist-v2.md](catdef-strategist-v2.md))
**Parent:** senior-open-standards-strategist v1.1.0 ([senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md))

## Disposition

`catdef-strategist.openthing` is bumped from v2.0.0 to v2.1.0 with OPD inheritance from the parent's new v1.1.0. The file at `roledefs/catdef-strategist.openthing` (and its `.json` mirror) is updated in-place. The `metadata.derived_from.version` is bumped 1.0.0 → 1.1.0 to track the parent.

## Rationale

This is the catdef-side of the OPD coordinated rollout. Per OPD decision M2, **existing pre-OPD derivations migrate by hand** because the self-bootstrap property only applies prospectively. catdef-strategist v2.0.0 was forked from sos-strategist v1.0.0 (pre-OPD); it has no OPD code paths and would never run an OPD check to discover the parent's new v1.1.0 OPD obligation via self-bootstrap. The migration is performed by hand in this v-bump.

## Changes from v2.0.0

OPD inheritance from parent v1.1.0 (preserved-from-parent additions; **NOT** prefixed CATDEF ADDITIVE):

| Component | Location |
|---|---|
| `output_contract.opd_review_memo` | New entry inserted at position 6 (after `forward_work_log_entry`, before the existing CATDEF ADDITIVE entries `catdef_build_directive` and `catdef_memory_update`) |
| OPD session-start conversation_rule | New entry inserted alongside existing parent conversation_rules (before existing CATDEF ADDITIVE rules) |
| `workflow.ongoing_professional_development` | New flat sibling key added between `pattern_promotion` and `engagement_close` |
| `metadata.opd_frequency_days: 7` | New metadata field added |

Mechanical updates:
- `roledef` stamp: 0.1.0 → 0.2.0
- `version`: 2.0.0 → 2.1.0
- `metadata.derived_from.version`: 1.0.0 → 1.1.0 (tracks parent)
- `metadata.history[2]` added for the bump

CATDEF ADDITIVE items from v2.0.0 (output_contract entries, conversation_rules, workflow phase additions, x.governance.* extensions, turing-test scenarios) preserved unchanged.

## Validation summary

- **Schema validation:** PASS — all v0.2.0 MUST fields present; all SHOULD fields present; OPD pattern conforms to SCHEMA.md §"Ongoing Professional Development (OPD)".
- **Additive-only invariant against parent v1.1.0:** PASS — every v1.1.0 parent item across all array fields preserved verbatim; the CATDEF ADDITIVE specializations from v2.0.0 also preserved.
- **derived_from URL resolution:** parent's URL resolves to current canonical (post-OPD-rollout this PR brings sos-strategist to v1.1.0 in canonical).
- **OPD will activate:** with parent now non-null and at v1.1.0, the next session of catdef-strategist will run OPD per the new workflow phase. State file will be created lazily at `<working-repo>/opd-state/catdef-strategist.json` on first run.

## Notes

- **Minor version bump (additive only).** v2.x → v2.1 reflects new optional behavior (OPD); breaking nothing. Adopters of v2.0.0 remain valid; the bump is opt-in per the SHOULD-pattern.
- Bot-identity provisional pending governance ratification.
- This decision lands as part of a **3-artifact coordinated rollout**: [senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md), this file, [roledef-strategist-v1-1.md](roledef-strategist-v1-1.md). All three land in the same PR.

## Cross-references

- OPD proposal decision: [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md) (especially M2)
- Schema reference: [SCHEMA.md §"Ongoing Professional Development (OPD)"](../SCHEMA.md#ongoing-professional-development-opd)
- Predecessor decision: [catdef-strategist-v2.md](catdef-strategist-v2.md) (v2.0.0 retrofit derivation)
- Parent decision: [senior-open-standards-strategist-v1-1.md](senior-open-standards-strategist-v1-1.md) (parent v1.1.0)
- Companion decision: [roledef-strategist-v1-1.md](roledef-strategist-v1-1.md) (sibling derivation rollout)
