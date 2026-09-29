# Quality management

**This is the record of what this definition decided to check, by which method, and how far.**

The list of aspects and stages, the baseline, and how to decide are in `docs/autodrive.md`
"Quality management," distributed by the reference implementation. **This is where the decided results go.**

## How this differs from others

**It is documents only, with no code.**

Therefore **the product quality grid does not apply as it is.** The stages of unit, integration, system,
acceptance, and production monitoring — none of them exist.

**Writing "does not apply" is itself a record.** Leave it blank, and later one cannot tell whether it was
undecided or unnecessary.

**The users are people who develop following this definition, and the reference implementation.** If the definition
contradicts itself, those following it act on the contradiction. **It is noticed when the contradiction shows up in an implementation.**

---

## What is checked

| Aspect | Stage | Method | How far it looks |
|---|---|---|---|
| Consistency | Static | **A human reads it** | Contradictions between sections. **There is no mechanical detection** |
| Consistency | Static | **None** | **Whether each `§n` reference points to a section that exists.** All currently exist, but it is a human who confirms this |
| Consistency | Static | **None** | **Whether the version in the body matches the top of CHANGELOG.** Currently both v0.17 |
| Structure of records | Static | `invariants --scope self` (CI) | **Only the required attributes and structure of records.** The content of the definition is not looked at |
| Drift between definition and implementation | — | **None** | The reference implementation's tests cite `定義§n` in 23 places, but **nobody looks at whether what they cite matches the definition** |

**Do not omit "how far."** The name of a method alone does not say whether it looks at everything or at a single file.
**Readers take it that ranges not written are being looked at too.**

## What was decided not to apply

**Do not force the rows of the grid to be filled.** Filling an axis that does not fit gets the filling used as an explanation of quality
(definition §10).

| What | Why it does not apply |
|---|---|
| Functionality (unit, integration, system) | **There is nothing that runs.** No executable target exists |
| Achieving the business purpose (acceptance, E2E) | Same as above. **Whether this definition's "purpose was served" shows up on the side of the reference implementation and the subject apps** |
| Performance | Same as above |
| Reliability (production monitoring) | **There is no production.** What is distributed is documents, and they do not run |
| Security (dependency vulnerabilities) | **There are no dependencies.** It is documents only |

## What was decided not to check

| What | Why it was left open | Condition for revisiting |
|---|---|---|
| Scanning for leaked secrets | The reference implementation's `invariants` looks across repositories, and **this repository is included in its targets.** Not held twice | When it is removed from the cross-repository check |
| Automatic detection of whether each `§n` reference points to a section that exists | **All currently exist.** It breaks when sections are added or removed, but that is infrequent. **Even if it breaks, a reader notices immediately** (a number that cannot be opened stands out) | When additions and removals of sections become frequent |

## What cannot be undone

| What could happen | What detects it | Who triggers |
|---|---|---|
| A contradictory definition is distributed, and those following it act on the contradiction | **None.** A human only reads it | Human (integration is the trigger) |
| What is integrated is published as it is (published on 2026-09-26 by human judgment. LICENSE is CC BY 4.0) | Secrets are looked at in two places. Files that must not be tracked and key shapes the reference implementation uses are checked by `invariants` on every submission. Keys in shapes GitHub can detect are stopped by Push protection at push time, and Secret Protection reports them including history (enabled by a human on 2026-09-26). **What neither looks at is secrets in undetectable shapes, and the judgment of whether the content is fine to publish** | Human (integration is the trigger) |

**If there is none, write "none."** A blank is read as "no problem."

## Levels decided

**It was the AI that proposed and the human that chose.** It must be readable who decided.

| Item | Level decided | When decided |
|---|---|---|
| Most of the grid | **Does not apply.** Not forced to be filled | 2026-09-23 |
| Consistency | **A human reads it.** No mechanical detection is placed | 2026-09-23 |

## Things noticed (while writing this record)

**The tags stop at `definition-v0.9`.** The body is at v0.16.

README says "For earlier versions, see the git tags (`definition-v0.4` and so on)," but
**there are no tags from v0.10 onward.** What the guidance points to does not exist.

**Not fixed in this record.** Handled in AUT-229. If not written down, the next reader will look into the same thing again.
