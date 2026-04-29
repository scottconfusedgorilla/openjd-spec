# Proposal Decision: Job placement and charter slots

**Disposition:** Accept with Modifications
**Origin:** [proposals/job-placement-and-charter-slots.md](../proposals/job-placement-and-charter-slots.md); originating memo [memos/2026-04-26-2230--orgdef-strategist--roledef-strategist--job-shape-package.openthing](../memos/2026-04-26-2230--orgdef-strategist--roledef-strategist--job-shape-package.openthing)
**Decided:** 2026-04-26 by roledef-strategist
**Conditional on:** [proposals/role-vs-job-distinction.md](../proposals/role-vs-job-distinction.md) reaching Accept status. The Job type this proposal extends is defined there; if that proposal lands materially different, this decision needs revision in lockstep.
**Cross-spec:** Companion proposal forthcoming in `orgdef-spec/orgdef-spec-org/proposals/charter-shape-additions.md` per the originating memo. This decision records the roledef-side resolutions; orgdef coordination follows.

## Disposition

The supplementary proposal — adding `placement` and `charter` to the Job type — is **Accepted with seven modifications** described in the Modifications section below. The empirical grounding (orgdef-spec dogfood, 2026-04-26) is strong, the synthesis framing relative to role-vs-job-distinction is correct, and the gap the proposal closes (machine-readable bootstrapping into a Job with zero null-context) is real. The modifications resolve the proposal's six open questions and reduce coupling with the parent proposal.

## Rationale

### Why Accept

The "completeness" criterion the proposal articulates — *a fresh AI runtime loaded with the orgdef + role + job bundle becomes the job, with zero context backfill* — is the right success bar for the catdef-family AI-charter pattern. The dogfood demonstrates that role-vs-job-distinction's Job type, as drafted, is incomplete against this bar in two specific ways:

1. **No structural placement.** A Job that doesn't know what it reports to, directs, or coordinates with cannot operate without backfilling from the user. That backfill is a hallucination invitation.
2. **No charter-vs-role disambiguator.** Two Jobs deriving from the same parent Role are indistinguishable on intrinsic content alone. The charter slot makes the disambiguation first-class and machine-readable.

Both gaps are visible in the dogfooded `orgdef-strategist` artifact, which had to fall back to `x.orgdef.placement` extension-namespace workarounds. That those workarounds emerged structurally (not from a-priori design) is exactly the empirical grounding the proposal review process exists to surface.

### Why Modifications

The proposal as drafted leaves six open questions explicitly inviting strategist call. Several of them are coupled — resolving one constrains the others. The modifications below resolve all six and tighten the field placement decisions to be consistent with parent-proposal patterns.

## Modifications

### M1. Move `charter` to top-level

Charter is **role-defining content** — a sibling of `identity`, `description`, and `voice`, not of `metadata.derived_from`. It states the stable slot of responsibility this Job fills, which is a defining property of the artifact, not an admin/lineage attribute about it.

**Resolves:** Open Question 3 (top-level vs metadata) for charter.

### M2. Move `placement` to `metadata.placement`

Placement is **structural reference info** — symmetric with `metadata.role_definition` and `metadata.org_definition` from the parent proposal. All three are "structural references to other artifacts" and belong in the same cluster.

The proposal's discoverability argument for top-level placement (a fresh AI reads top-level fields first) doesn't survive scrutiny: a machine reader parses the entire artifact in one pass; "looks first at top-level" is human-reader thinking. Symmetry with parent-proposal metadata fields wins.

**Resolves:** Open Question 3 (top-level vs metadata) for placement.

### M3. Drop `placement.org`; rely on `metadata.org_definition`

The `placement.org` sub-field duplicates `metadata.org_definition` from the parent proposal. Single source of truth at the metadata level. The remaining placement sub-fields (`reports_to`, `directs`, `coordinates_with`) live in `metadata.placement`.

**Resolves:** Open Question 1 by removing the duplication that motivated it.

### M4. Defer `peer_to` to v0.2

`peer_to` was not exercised by the dogfood. `coordinates_with` covers the case for v0.1 (co-strategists, co-leads can list each other under coordinates_with for now). Promote `peer_to` to a first-class sub-field if real demand surfaces from a corpus of operational jobs.

**Resolves:** Open Question 4 → defer.

### M5. Charter as prose (string or polymorphic) for v0.1

Agree with the author's weak preference. Structured charter (`{purpose, scope, accountability, success_criteria}`) may emerge as a v0.2 promotion if patterns surface across a meaningful corpus of operational charters. v0.1 keeps the friction floor low.

**Resolves:** Open Question 5 → prose.

### M6. SSoT rule: `orgdef.relationships` authoritative; `metadata.placement` is a denormalized snapshot

When both `metadata.placement` (Job-side) and `orgdef.relationships` (Org-side) express the same structural facts, **`orgdef.relationships` is authoritative**. `metadata.placement` is a **denormalized snapshot for AI cold-start convenience** — populated at job-authoring time and kept in sync going forward.

Validator behavior:
- SHOULD verify consistency when both are accessible (public-public case)
- MAY skip verification with WARN (not FAIL) when one or both reside in private repos that the validator cannot resolve

This rule keeps the org-graph as the single point of truth while preserving the cold-start ergonomics that motivated job-side placement in the first place. Per the parent proposal's "snapshot semantics consistent with roledef's git-fork derivation model," this is the right shape.

**Resolves:** Open Question 1 → orgdef wins, with explicit consistency-check semantics.

### M7. Naming: keep `placement`

Empirically anchored from the dogfood (`x.orgdef.placement`). Alternatives offered (`context`, `org_position`, `position_context`) are weaker — `context` collides with conversation-context terminology, the others are verbose and conflict with `orgdef:Position`. Name stays.

**Resolves:** Open Question 2 → no change.

## Final shape (post-modifications)

The Job artifact gains:

```json
{
  "charter": "<prose, 1–4 sentences, polymorphic per catdef i18n rules>",
  "metadata": {
    "role_definition": { "id": "...", "version": "...", "url": "..." },   // from role-vs-job-distinction
    "org_definition":  { "id": "...", "version": "...", "url": "..." },   // from role-vs-job-distinction
    "placement": {
      "reports_to":        "<job-id or position-id, optional>",
      "directs":           ["<job-id>", "..."],
      "coordinates_with":  ["<job-id>", "..."]
    }
  }
}
```

All `metadata.placement` sub-fields are individually optional. `metadata.placement` itself is OPTIONAL but RECOMMENDED for any operational job. `charter` is OPTIONAL but RECOMMENDED — sample/template Jobs in the canonical library MAY omit it.

Cardinality of `placement.reports_to`: zero-or-one (per the proposal's table).

## Build directive (for roledef-maintainer)

When `role-vs-job-distinction` reaches Accept status and schema work begins, the maintainer drafts roledef SCHEMA.md changes that:

1. Add `charter` as a top-level OPTIONAL/RECOMMENDED field on the Job type (string or polymorphic per catdef i18n rules)
2. Add `metadata.placement` as an OPTIONAL/RECOMMENDED object on the Job type with sub-fields `reports_to` (zero-or-one), `directs` (zero-or-many), `coordinates_with` (zero-or-many)
3. Document the SSoT rule (orgdef.relationships authoritative; metadata.placement denormalized) in SCHEMA.md alongside the `metadata.placement` definition
4. Add validator rules per M6 (SHOULD verify consistency when both accessible; MAY WARN-skip for private)

Conformance fixtures requested by the proposal:

- `valid_jobs/job-with-full-placement.openthing` — exemplar with all `metadata.placement` sub-fields populated
- `valid_jobs/job-with-minimal-placement.openthing` — exemplar with empty `metadata.placement` (just present, no sub-fields)
- `valid_jobs/job-with-charter-only.openthing` — exemplar with `charter` but no `metadata.placement` (e.g., a solo job)
- `invalid_jobs/job-placement-references-nonexistent.openthing` — counterexample where `metadata.placement.reports_to` doesn't resolve in the referenced orgdef
- `invalid_jobs/job-placement-circular.openthing` — counterexample where `metadata.placement` creates a `reports_to` cycle

The version-bump rolling with role-vs-job-distinction acceptance covers this proposal too — no separate roledef schema version bump.

## Cross-spec coordination

The companion orgdef proposal (`orgdef-spec/orgdef-spec-org/proposals/charter-shape-additions.md`, sketch only per the originating memo) extends orgdef SCHEMA with vision/values/red_lines/recommended_patterns slots and needs to declare the SSoT rule in its own terms. Since the same head holds both strategist seats during the bootstrap, that proposal will be drafted-and-decided in coordinated sessions; the artifact trail still goes through both repos' `proposals/` and `decisions/` directories per role discipline (no shortcut routing just because one head holds both seats).

## Migration of dogfood artifact

Per the proposal's acceptance criteria: `orgdef-spec/orgdef-spec-org/jobs/orgdef-strategist.md` migrates from markdown + `x.orgdef.*` test-namespace extensions to formal `.openthing` JSON with `charter` (top-level) and `metadata.placement` (per the post-modifications shape) once role-vs-job-distinction lands.

## Companion roledef submissions

The package included two roledef submissions ([proposed-roledefs/org-creation-facilitator.openthing](../proposed-roledefs/org-creation-facilitator.openthing) and [proposed-roledefs/job-creation-facilitator.openthing](../proposed-roledefs/job-creation-facilitator.openthing)). They are typed as `roledef:Role` (not Job-typed), so they do NOT depend on either pending proposal's acceptance for their own validity.

**Strategist sign-off recorded for both:**

- Schema-conformant (catdef envelope, all MUST fields, valid `roledef:Role` type, no reserved-namespace misuse)
- Filename = id; IDs unique vs current `roledefs/` and `proposed-roledefs/` directories
- Scope appropriate (universal facilitator pattern, not project-specific); library fit strong (these are the on-ramp for orgdef + role-vs-job adoption)
- Quality clean (tight guardrails, concrete examples, clear voice, no contradictions)

Two minor flags observed; neither blocks promotion:

- Author email is `orgdef-strategist@orgdef.org` rather than `roledef-strategist@roledef.org`. Defensible — they're authored from the orgdef seat — though housing them in the roledef canonical library arguably warrants a co-author line. Maintainer's call at promotion time.
- `metadata.derived_from: null` despite borrowing methodology from `senior-jaded-vc-associate v1.0.0`. Author explicitly distinguishes "register replaced" from "structural inheritance." Strategist agrees: derivation is a structural claim, not an inspiration claim. Pass.

`job-creation-facilitator`'s `output_contract` describes an artifact shape that depends on the two pending proposals. Forward-compatible by design (markdown form during bootstrap, formal `.openthing` once Job type lands). Acceptable.

**Recommendation to roledef-maintainer/validator:** route through standard two-stage workflow. Validator runs Turing-test fixture pass; if clean, maintainer promotes both at merge with the standard `decisions/<id>.md` artifacts at promotion time. No further strategist gate required for these two unless validation surfaces something material.

## References

- Originating proposal: [proposals/job-placement-and-charter-slots.md](../proposals/job-placement-and-charter-slots.md)
- Parent proposal: [proposals/role-vs-job-distinction.md](../proposals/role-vs-job-distinction.md) (currently Open for discussion; this decision is conditional on its acceptance)
- Originating memo: [memos/2026-04-26-2230--orgdef-strategist--roledef-strategist--job-shape-package.openthing](../memos/2026-04-26-2230--orgdef-strategist--roledef-strategist--job-shape-package.openthing)
- Empirical exemplar: `orgdef-spec/orgdef-spec-org/jobs/orgdef-strategist.md` (dogfood artifact, will migrate to formal shape post-acceptance)
- Companion roledefs: [proposed-roledefs/org-creation-facilitator.openthing](../proposed-roledefs/org-creation-facilitator.openthing), [proposed-roledefs/job-creation-facilitator.openthing](../proposed-roledefs/job-creation-facilitator.openthing) (strategist-cleared; awaiting validator pass + maintainer promotion)
