# jds/

The canonical openjd library. Each `.openthing` file in this directory is a Job Description that has passed validation, Turing test, and maintainer review.

JDs reach this directory through the [two-stage submission workflow](../CONTRIBUTING.md#1-jd-submissions): submitted to `proposed-jds/`, validated, reviewed, and promoted here at merge time.

Each JD here is:

- A valid catdef `.openthing` per [SCHEMA.md](../SCHEMA.md)
- Passed the Turing test on at least one runtime
- MIT-licensed (or with a license deviation flagged in `metadata.license`)
- Indexed in [`../catalog.opencatalog`](../catalog.opencatalog)
- Decision-recorded in `../decisions/jd-<id>.md`

To consume a JD from this directory, use the `openjd-load` Claude Code skill (in development) or paste the file's contents into a fresh AI session as a system prompt.

---

*This directory's contents are the canonical openjd reference library. Forks welcome; deviations are encouraged.*
