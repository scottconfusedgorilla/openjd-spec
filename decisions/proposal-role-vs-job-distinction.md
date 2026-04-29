# Proposal Decision: Role-vs-Job Distinction

**Disposition:** Accept with Modifications
**Origin:** [proposals/role-vs-job-distinction.md](../proposals/role-vs-job-distinction.md) (PR #23)
**Decided:** 2026-04-26 by roledef-strategist
**Composes with:** [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md) (filed in tandem; OPD applies at every tier including the new Job tier)
**Unblocks:** [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md) (filed 2026-04-26; was conditional on this proposal's acceptance — condition now satisfied)
**Cross-spec:** orgdef artifact update (catdef-org's `position.role_definition` → `position.job_definition` migration) tracked as follow-on

## Disposition

The role-vs-job split is **Accepted with eight modifications** — one per open question — that resolve the proposal's outstanding decision points and lock in the field shape. The empirical case (eight bootstrap-era artifacts, four of which are clearly job-shaped per the human steward's framing) is strong; the privacy story is the load-bearing rationale that does not generalize without the split; the migration story is workable.

## Rationale

### Why Accept

The conflation of Role and Job under one type was tolerable for the catdef-family bootstrap (everything is open) but actively obstructs the broader use case roledef is designed to enable: commercial / private orgs hosting their own jobs without dumping internal specifications into a public canonical library. The author's framing — *"the same org may have the same role in multiple jobs within the org"* — is exactly the right granularity. A `roledef:Role` is the abstract; a `roledef:Job` is the concrete instantiation; positions reference jobs.

The three things this enables (same-role-multiple-jobs, job-tier-privacy, authorization-implies-access symmetry) are not achievable via convention alone — the type discriminator is load-bearing for validators, renderers, and the curation workflow. Alt 4 (convention only, no new type) loses that machine-readability and is correctly rejected.

### Why Modifications

The proposal explicitly invites strategist call on eight open questions, each tied to a non-trivial design decision. Resolving them inline tightens the spec and avoids deferred-decision drift. None of the modifications are adversarial to the proposal's framing — most align with the author's weak preferences.

## Resolutions

### M1. OQ#1 → `roledef:Job` under the roledef namespace (Alt 2)

The proposal offered Alt 2 (`roledef:Job` under roledef) versus Alt 3 (`jobdef:Job` as a new catdef-family consumer-spec sibling). Author's weak preference was Alt 2 for v0.1.

**Strategist call: Alt 2.** Roles and jobs are inherently coupled — a job presupposes a role; the spec rules for one substantially overlap with the spec rules for the other. Spinning up a new spec org (`jobdef-spec/jobdef`) costs more than the conceptual gain and risks drift between two repos that need to evolve in lockstep. The catdef-family pattern is "one spec per truly independent domain"; jobs are not independent of roles.

If patterns surface that demand separation in a future v0.x, a cleave is straightforward (publish a `jobdef:Job` namespace alongside a backward-compat `roledef:Job` alias). YAGNI argues against premature spec separation.

### M2. OQ#2 → Incremental migration

**Strategist call: incremental.** Big-bang migration breaks consumers without warning; incremental migration with deprecation notices preserves existing canonical URLs and gives adopters time to update at their own pace. This matches the proposal's phrasing *"existing artifacts can be left at their current canonical URLs ... no big-bang restructuring required."*

Practical sequencing: schema lands first (Job type, `position.job_definition` field), then bootstrap-era artifacts are mirrored to `sample-jobs/` and operational copies are published to owning project repos in any order. Existing `roledefs/` entries for the four job-shaped artifacts remain (with `metadata.history` notes that they're being formalized as samples) until a future cleanup PR removes the duplicates.

### M3. OQ#3 → Option (c) for all four bootstrap-era artifacts

The four job-shaped bootstrap-era artifacts (catdef-strategist v2.0.0, roledef-strategist v1.0.0, sncro-blackhat-tester, senior-jaded-vc-associate) **all get option (c): both sample-of-record AND operational copy.**

**Strategist call: (c) for all four.** Author's weak preference. The pedagogical value of these artifacts as worked examples (canonical-implementor, derived strategist, abstract→derived showcase, production-extraction) is real; canonical sample-jobs/ retention preserves the institutional record. Operational copies in owning project repos serve their actual job-holder role. The two are not redundant — they have different purposes (curation vs operation) and different lifecycles (sample is pinned at the published version; operational evolves with the project).

### M4. OQ#4 → `position.job_definition`

**Strategist call: `position.job_definition`.** Symmetric with the existing `position.role_definition`. Adopters reading the orgdef schema see the parallel immediately. Alternatives (`instance_of`, `fills`) are clever but less obvious; this isn't the place to be clever.

### M5. OQ#5 → Location-only privacy; no `x.job.commercial_sensitivity` field

**Strategist call: location only.** The repo's public/private status IS the privacy signal. Adding an explicit field creates two sources of truth (file-in-private-repo vs flag-says-public-but-it's-in-private-repo) and invites mismatches. Per the proposal's own discussion, *"how private package-manager dependencies work"* — the parallel holds: NPM doesn't have a "this package is private" flag inside the package.json; the registry-level visibility IS the flag.

If a use case surfaces where a public-repo job needs to be flagged as commercially-sensitive-but-published-anyway, that's an extension-namespace question (`x.job.sensitivity`) — not a core spec field.

### M6. OQ#6 → Renderer non-issue, no spec change

**Strategist call: confirm at implementation time, no spec change.** render.catdef.org handles arbitrary catdef-family namespaced types per catdef.org PR #2. roledef:Job is just another type from the renderer's perspective. The acceptance criteria item for renderer updates is removed.

### M7. OQ#7 → orgdef follow-on tracked, not blocking

**Strategist call: track as follow-on, do not block this acceptance.** The catdef-org orgdef artifact will need to be updated as part of the cross-spec coordination work. That's a separate PR in the orgdef substrate; this acceptance does not gate on it.

The acceptance does, however, place a marker: orgdef SCHEMA.md needs to add `position.job_definition` as an OPTIONAL field alongside `position.role_definition`. That's a roledef-side ask of orgdef; the orgdef-strategist seat will field it.

### M8. OQ#8 → Authorization-implies-access for jobs documented explicitly

**Strategist call: document explicitly in CONTRIBUTING.md.** Yes — jobs live in their owning project's working repo, same discipline as memos. CONTRIBUTING.md should add a "Jobs in working repos" section that articulates:

> A job artifact lives in the working repo of the org that defines it, at `<project>/<org-name>-org/jobs/<job-id>.openthing` (or `<project>/jobs/<job-id>.openthing` for orgs with no nested-org-folder convention). This mirrors the catdef-org memo convention's per-recipient working-repo discipline: authorization to read and modify job artifacts is implied by repo access. Public projects publish jobs in public repos; private orgs keep them in private repos.

Validators MAY skip job-resolution checks for jobs whose URL pattern indicates a private repo (with WARN, not FAIL).

## Build directive (for maintainer / strategist + maintainer in tandem during bootstrap)

Per CONTRIBUTING.md the maintainer drafts spec text in support of strategist decisions. roledef does not currently have a separate maintainer; the strategist drafts the spec changes during bootstrap. Sequencing:

1. **roledef SCHEMA.md update** — define `roledef:Job` type, required fields (`job` id, `name`, `version`, `metadata.role_definition`, `metadata.org_definition`), additive-only-vs-parent invariant, sample-jobs/ directory convention. Coordinate with the just-decided `job-placement-and-charter-slots` package: include `charter` (top-level, prose) and `metadata.placement` (per the modifications in [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md)) as part of the same Job-type schema definition.

2. **roledef CONTRIBUTING.md update** — add the "Jobs in working repos" section per M8; add a job-submission workflow section noting that NOT all jobs flow through the canonical library (many live only in adopter repos).

3. **`sample-jobs/` directory** — create directory with README explaining the curation-vs-operation distinction.

4. **Conformance fixtures** — `valid_jobs/canonical-implementor-job.openthing`, `valid_jobs/job-with-private-org.openthing`, `invalid_jobs/job-without-role.openthing`, `invalid_jobs/job-with-fork-not-derivation.openthing` — per proposal §"Conformance Tests".

5. **Validator updates** — `metadata.role_definition.url` resolution check (for public-repo jobs), additive-only-vs-parent invariant verification, MAY-skip-with-WARN for private-repo jobs.

6. **Migration of existing four job-shaped artifacts** — as separate PRs, in any order:
   - catdef-strategist v2.0.0 → mirror to `sample-jobs/catdef-strategist.openthing`; publish operational at `catdef-spec/jobs/strategist.openthing` (catdef-spec org; the operational instance)
   - roledef-strategist v1.0.0 → mirror to `sample-jobs/roledef-strategist.openthing`; publish operational at `roledef-spec/roledef/jobs/strategist.openthing`
   - sncro-blackhat-tester → mirror to `sample-jobs/sncro-blackhat-tester.openthing`; publish operational at `sncro/jobs/blackhat-tester.openthing`
   - senior-jaded-vc-associate → mirror to `sample-jobs/senior-jaded-vc-associate.openthing`; publish operational at `dangerstorm/jobs/vc-associate.openthing`

7. **catdef-org orgdef artifact follow-on PR** — add `position.job_definition` OPTIONAL field; update existing positions that reference jobs to use it. Files in catdef-org repo; coordinated with the orgdef-strategist seat.

8. **roledef-spec/roledef working-repo dogfood** — once schema lands, this repo gains a `jobs/` directory with `roledef-spec/roledef/jobs/strategist.openthing` (the operational copy of the roledef-strategist job).

## Cross-proposal composition

This decision lands in tandem with [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md). OPD applies at every tier of the role-vs-job hierarchy per OPD's §"Interaction with the role-vs-job-distinction proposal". The two compose naturally; no additional integration work is needed at the schema level (each tier independently runs its own OPD against its immediate parent).

The just-decided [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md) is now unblocked. The Job type defined here gains `charter` (top-level) and `metadata.placement` per that decision's M1–M7. The build-directive items in that decision and this one are interleaved; the maintainer (or strategist drafting) implements them as a single Job-type schema-definition PR.

## Version implications

This is a **minor version bump** to roledef (0.1.0 → 0.2.0). Additive: new type (`roledef:Job`), new optional field (`position.job_definition` in orgdef), new directory (`sample-jobs/`). Existing `roledef:Role` artifacts unaffected. No breaking change to consumers; reader-lenient runtimes ignore unknown fields.

## References

- Originating proposal: [proposals/role-vs-job-distinction.md](../proposals/role-vs-job-distinction.md)
- Originating PR: #23 (merged 2026-04-26)
- Companion decision: [proposal-ongoing-professional-development.md](proposal-ongoing-professional-development.md)
- Unblocked decision: [proposal-job-placement-and-charter-slots.md](proposal-job-placement-and-charter-slots.md)
- Cross-spec coordination: orgdef substrate (catdef-org orgdef artifact), follow-on PR
