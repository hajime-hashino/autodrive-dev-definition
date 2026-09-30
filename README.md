# AI Autodriving Development — Definition

> **Version**: v0.20
> **Last updated**: 2026-09-30
> For earlier versions, see the git tags (`definition-v0.4` and so on). The differences between versions are in [CHANGELOG.md](./CHANGELOG.md).

## 1. One-sentence definition

A development method that removes the human review gate by replacing error detection by human review with execution results and automated verification, and in which the AI leads **both the building of the product and the improvement of the machinery that builds it**. The human role is limited to presenting the What/Why and making decisions at the points where the AI stops.

### What it is for

**To reach a state in which the quality of what is produced does not depend on the skill of the person driving the work.**

As long as a human holds the review gate, quality is proportional to the skill of whoever looks. **For someone without that skill, being able to build fast is simply a hazard.** Volume grows while there is no means of confirming that what was built is correct.

Detection is replaced with execution results and automated verification **in order to take the job of confirming off the human.** What §1 says it removes is the gate, not the confirmation.

**This does not mean "anyone can build the same thing."** The upper limit of the difficulty and quality of the product that can be reached varies with the person driving it (§12). The aim is not to equalize that limit. **It is that, within the range a given person can reach, quality is not swayed by skill.**

#### Relation to §4

This is a purpose, not a variable to maximize. **The variable to maximize is kept to the single one in §4.**

With two, there would be two criteria in situations that require choosing between them. §4 choosing "output per unit of human involvement" is a consequence of this purpose. **If skill cannot be assumed, the only option is to reduce human involvement per unit of output.**

#### Not measured

**This purpose is not measured directly as a metric.** Beyond the same reasons as the variable in §4, "a certain level of quality" cannot be defined per person or per product. **Put a standard that cannot be measured into a metric, and it can be declared achieved.**

**Accordingly, there is currently no means of judging whether the purpose has been achieved.** Holding it up as a purpose and being able to judge it are separate things. Do not write something that cannot be judged as if it could be.

How to catch signs that run against the purpose is undecided (§18).

## 2. Scope

It does not stop at local implementation; it covers deployment to the verification environment, pre-release testing, release preparation, and the release itself. Without release, the outer loop does not close (because execution results are the only material for improvement).

## 3. Applicability

Initially, only new development is in scope. Application to existing services will be considered later (the expectation is to enter through partial application per feature or per module).

New development and existing services lack different application conditions, so they are treated as separate problems, not as a difference in difficulty.

| | New development | Applying to an existing service |
|---|---|---|
| Detectable | The harness is grown from zero. Weakest at the start | Existing tests and production monitoring can be used to some extent |
| Reversible | No users, so broadly reversible | Running, so the reversible range is narrow |
| Recorded | Can be built in from the start | Retrofitted |
| Other | — | Understanding existing code, implicit specifications, absence of past design decisions |

## 4. The variable to maximize

Not development volume per unit of time, but **output per unit of human involvement**.

"It feels safer if a human looks" worsens this variable, so as a rule it is not adopted.

### What to reduce is "checking for reassurance" and rework, not dialogue

**Discussing things thoroughly is necessary to build a good product.** Asking what to build, deciding how it looks, agreeing on the order — these are human involvement, but **they do not worsen this variable.** If they reduce rework, involvement per unit of output actually goes down.

What worsens it is these two.

- **Checking for reassurance.** A human looks again even though a means of detection already exists
- **Rework.** The understanding was different; something has to be rebuilt

**Drop this distinction and even necessary dialogue gets cut.** Reading it as "reduce stops," one proceeds without confirming requirements and ends up rebuilding later. That is the opposite of what §4 asks for.

How this is treated as a kind of stop is in §6.

This is, however, a principle for judgment, and **it is not measured directly as a metric**. Human involvement time depends on self-reporting, and those recording it gain nothing from doing so, so it becomes a formality. Data that cannot tell missing values from measured ones misleads judgment more than having no data.

When explaining the effect externally, use per-engagement after-the-fact totals (the effort of the people involved, the AI's token consumption, the size of the system built). Existing means of totaling suffice, so there is no need to build measuring equipment.

## 5. Structure: two loops

### Inner loop (building the product)

Plan → implement → verify → release.

Human checkpoints may sit on it, but they are **movable**, and the outer loop removes them.

### Outer loop (fixing the machinery)

Observe → generate improvement proposals → human approval → apply.

The harness, skills, and the scope of delegation itself are the targets of improvement. The AI runs both loops; what the human approves is only the application of the outer loop and the stopping points of the inner loop.

### Separating inner from outer

Changes to the harness appear in both loops. They are distinguished by **whether they move a cell in the delegation table (§8)**.

| Change | Loop |
|---|---|
| Adding tests along an existing means of detection | Inner. Coverage widens, but detectable / not detectable does not move |
| Introducing a new means of detection (visual regression, accessibility checks, etc.) | Outer. Moves an undetectable area to the detectable side |
| State transitions of an area (on hold / under observation / delegated) | Outer |

**Harness coverage widening is inner; harness capability changing is outer.** Treating everyday test additions as outer too would concentrate human approval in the bootstrap phase, contrary to §4.

Note that when an improvement can be generalized, where it lands splits by repository. The delegation table and the project-specific harness land on the product side; skills land on the reference implementation side. This is a separate question from where the outer loop runs.

## 6. What telemetry records

Telemetry is the input to the outer loop. Therefore **it records only what is used for improvement decisions**. Metrics for explanation are not included.

### Must be recorded

| What is recorded | Used for |
|---|---|
| Kinds and frequency of stop events | Kinds that recur are candidates for skills and automation. **Count input and rework separately** (below) |
| Rework, and the breakdown of its causes (requirements drift / design drift / implementation bug) | Identifying which stage has low accuracy |
| Missed detections (errors found in a later stage or in production) | Identifying weaknesses in the harness |
| Results of spot checks (range looked at, range not looked at, whether anything was corrected) | Judging loosening (§8). Understanding how undeveloped the harness is |
| Delegation changes, and whether anything failed afterwards | Verifying that loosening thresholds are sound |

**What the five have in common is that they point directly at where the harness is weak.** Missing any one, the outer loop cannot choose where to fix.

Lead time (from filing to production release) can be derived after the fact from Tracker and Repo timestamps. No equipment is set up to measure it.

### May be recorded

| What is recorded | Used for |
|---|---|
| Token consumption and cost | Material for estimates. Understanding tendencies of work where exploration drags on. Reviewing the model configuration |

**Do not treat its absence as a defect.** It may be recorded, but the judgment of whether something is active (§9) is not failed on the presence or absence of tokens.

**It is different in nature from the five above. Token consumption is a quantity; it does not point at what to fix.** Since the opening of §6 says "records only what is used for improvement decisions. Metrics for explanation are not included," if it is not used for decisions, there is no ground for keeping it among what must be recorded.

**This is confirmed by the record.** When the outer loop was run once around in the reference implementation, the ground for moving the delegation table was the breakdown of missed detections, and **the change history contained not a single mention of tokens.** Of 473 record lines, 233 (49%) were tokens, and over roughly a month of accumulation they were used for a decision 0 times.

Being something that must be recorded also created a structural problem. **Token consumption comes from the runtime, and is written after the work item's submission.** The five above are written during the work and so ride the submission; this one alone does not. A mechanism that picks it up later can compensate only when the same workspace remains, and **it is incompatible with parallel execution that creates and discards a workspace per work item.**

**If it is not something that must be recorded, no compensating mechanism is needed.** Whatever is missed does not appear in the records, and that is fine.

#### This is a separate matter from the model identifier

**Do not treat token consumption and the model used as a single bundle.** Both come from the runtime, but **the model identifier is a required attribute (below); only token consumption and cost became optional.**

Written as a bundle, stopping one drops the other too. Without the model identifier, even recording the five required items does not allow comparing model configurations.

### There are two kinds of stop

**Not every stop is something to reduce.** Lumping them together cuts even necessary dialogue.

| Kind | Examples | Treatment |
|---|---|---|
| **Stop to obtain input** | Hearing what to build, deciding how it looks, agreeing on the order, issuing credentials | **The method is working correctly.** Not something to reduce |
| **Stop due to rework** | The understanding was different, something has to be rebuilt, it was sent back at approval | **Something to reduce.** Shows which stage has low accuracy |

At the time of recording, make it possible to tell which one it is. **Reclassifying later brings in the interpretation of whoever reclassified** (the same reason as the breakdown of rework causes).

#### Asking the same thing again is rework, not input

**Without this safeguard, one can keep stopping by insisting "it's dialogue, so it need not be reduced."**

If something once decided is being asked again, the decision was either not kept or not read. **Do not count it as an input stop; treat it as rework.**

### Required attributes of events

Every event carries the **work item ID** and the **identifier of the model used** as attributes.

Without the former, per-ticket analysis is impossible; without the latter, there is nothing to compare when reviewing the model configuration. Neither can be attached retroactively, so they are required from the moment recording starts.

#### Exchanges not attributable to a work item

Deliberating whether to raise a work item, or requests before anything has been filed, **belong to no work item.** Records that come from the runtime (token consumption) are still produced.

**These are not §6 events.** The required attributes above are imposed on the targets §6 lists for recording, and exchanges that belong to no work item are not such targets in the first place. Treating them as §6 events missing attributes would make correctly working records appear as defects, and since the attributes cannot be attached retroactively, it would become a state that cannot be resolved.

**When recording token consumption**, satisfy the following two points. If the choice is not to record it, these two points do not apply.

- **Do not discard the records.** The cost is real. Keep them in a container separate from the §6 event stream, together with the reason they could not be attributed
- **Keep the count readable.** An increase in unattributed exchanges is a signal that exploration before raising a work item is growing, and is material the outer loop reads

**Whatever can be attributed must be linked.** Treating something as unattributed is allowed only when there is no way to link it in the mechanism. If one could omit attributes by declaring "could not attribute," being required would lose its meaning.

#### How strict to be

Bringing unattributed exchanges to zero would require a mechanism that prevents starting work without filing. **Whether to set one up is judged on cost and effect.**

This definition's position is that **not setting one up is the default.** The unattributed portion is small relative to the whole, and the effort spent making it strict works better for §4 (output per unit of human involvement) when directed elsewhere. **Link whenever possible; give up on what cannot be linked in the mechanism.**

This is not abandoning measurement. Since the count is readable, the judgment can be redone as soon as the amount can no longer be ignored.

### Caution on handling token consumption

Token volume is not a proxy for effort. The same amount means different things depending on whether it was spent exploring ambiguous requirements or implementing straightforwardly.

When used for estimates, do so by referring to the actual results of work items of similar nature; do not convert to effort as unit price times volume.

## 7. Application conditions

| Condition | Meaning |
|---|---|
| Reversible | Even if an error gets out, it stays within a range that can be undone |
| Recorded | The process's own blockages and failures remain, and can be made targets of improvement |

**"Detectable" (errors surface automatically, quickly, before getting out) is not a precondition but the first thing the outer loop grows.** In the early stage of new development no harness exists, so making it a condition would make the method impossible. The top priority in the bootstrap phase is building means of detection.

Note that the effort of rebuilding is not included in the conditions. Under this method the AI does the rebuilding, so it nearly disappears.

## 8. Operating the scope of delegation

**Put in a table how much is entrusted to the AI. This is called the "delegation table."**

It used to be called the "review boundary" or "boundary table," but **it was unreadable what it was a boundary of** (AUT-133). What it refers to has not changed.

The file names the reference implementation places (`boundaries.yaml`, `boundary-changes.md`) are not changed. **It would break places already distributed to, and the file names are not what made it hard to read.**

The scope of delegation is determined by two axes, **detectable** (how easily errors are detected automatically) and **reversible** (whether an error that gets out can be undone), and it moves over time.

|  | Not detectable<br><span style="font-weight:normal">Unknown unless a human looks<br>(broken UI, awkward wording)</span> | Detectable<br><span style="font-weight:normal">Tests or types fail<br>(failures appear automatically)</span> |
|---|---|---|
| **Not reversible**<br>Money transfer, production data deletion, external publication | Fixed condition | Automated gate + human trigger |
| **Reversible**<br>Gone with a rebuild or redeploy | Spot check | Full delegation |

Remember it as **not reversible × not detectable = fixed condition**.

### Operating rules

- Areas are set at **a granularity at which judgment and recording are unambiguous** (e.g. operation × target). The granularity itself may be decided on the reference implementation side
- Each area has three states: "on hold → under observation → delegated"
- **Loosening**: if N consecutive times under observation pass without human correction, move to delegated
- **Tightening back**: if a failure occurs while delegated, return to under observation. Loosening and tightening back are always operated as a pair
- For changes, **the AI proposes, the human approves, and the history is always kept**
- Human involvement does not converge to zero; it gathers and settles in the top-left, top-right, and bottom-left. Read the remaining involvement as an indicator of where the means of detection is weak

#### Approval is derived from the fact of "Integrate change"

**Do not express approval by declaration.** A history saying "approved" lets whoever wrote it claim approval. It is the AI that moves the scope of delegation, and making that approval writable by the AI turns the delegation table into self-reporting.

**The fact that "Integrate change" (§16) was executed** on a change that moved the scope of delegation is taken as approval. This operation is in the irreversible category and requires a human trigger. Therefore the AI can get only as far as proposing.

- **Do not keep a separate record of approval.** Repo holds the fact of integration, and holding it twice means only one side gets updated and they disagree (the same reason as the recording conventions in §16)
- **The first change that places the table is subject to approval but is not evidence of the loop having started.** Preparing the table and moving the table are different things
- The credentials used for judgment need **permission to read whether something was integrated**. If they cannot read it, the outer loop cannot be judged to have started. **Do not treat a state that cannot be judged as passing** (§9)

The granularity of what counts as "a cell moved" may be set on the reference implementation side. However, **it must be derivable from the diff of the delegation table itself, not from a declaration.**

### Spot checks

The target is **limited to how the working product looks**. Code and design documents are not included (§10).

The purpose is not exhaustive discovery of errors but observing whether the area may be loosened to delegated. Since errors in these areas can be undone, not every one needs to be found.

**The human may choose what to look at.** If they choose the places they want to see, look, and still make no correction, that is stronger evidence than choosing at random. Selection bias acts in the direction of making loosening stricter.

The following two points must, however, be satisfied.

- **The range not looked at remains in the record.** A record that cannot distinguish not having looked from having looked and found no problem misleads judgment (the same reason as §4)
- **Do not calculate a correction rate as a metric.** A rate obtained from a biased selection has no well-defined denominator. Loosening is judged by N consecutive times without correction

Targets risky enough to need confirmation every time are put on hold, not spot-checked. Errors missed by spot checks and found in a later stage are recorded as missed detections (§6).

#### Selection is a declaration of the scope of delegation

An area that is not continuously looked at is delegated, whether declared or not. Therefore a gap between actual checking behavior and the state of the scope of delegation is a signal the outer loop should read.

| Gap | How to read it |
|---|---|
| Delegated, but a human keeps looking | Loosened too early. A candidate to return to under observation |
| Under observation, but nobody is looking | Implicitly delegated. The same kind of accident as, in §9, something not active passing as active |

#### Two kinds of target

| Kind | Examples | Treatment |
|---|---|---|
| Temporarily not detectable | Broken layout, rendering of notifications | A target for harnessing. Fix the check result as a baseline and move it to the "detectable" side |
| Permanently not detectable | Soundness of sort order, naturalness of wording | No oracle. Remains steadily |

The amount accumulating in the former shows how undeveloped the harness is. If it does not decrease, read it as the outer loop not functioning.

## 9. Fixed conditions and invariants

### Fixed conditions

Destructive operations on production data, movement of money, irreversible external publication. Not loosened no matter how much track record accumulates.

### Invariants

- The outer loop starts and keeps running
- Telemetry is recorded
- Delegation changes stay in the history
- The AI cannot disable any of these

Invariants are built in **as the default behavior of the harness**, not as human work. This state is called **active**. It refers to a state that is maintained without anyone being conscious of it, and that cannot be forgotten.

#### Exception for the bootstrap phase

The work of building the recording machinery does not yet have that machinery. Therefore, only in the bootstrap phase, substituting invariants by human hand is allowed. The following two points must, however, be satisfied.

- The fact of substitution is left in the records
- **A state in which an invariant is not active can be detected mechanically**. A declaration alone cannot prevent the accident of operating on without it ever being connected

A state in which it cannot be judged whether something is active is itself treated as a failure.

An invariant takes one of the following four states. **Each says what something currently is.**

| State | Meaning | Failure? |
|---|---|---|
| **Active** | Working as machinery | |
| **Substituted** | Not machinery yet; a human is covering for it. **The fact of the substitution is in the records** | |
| **Unresolved** | No record of anyone covering it, or it cannot be judged whether it is active | **Failure** |
| **Out of scope** | Not handled within the range of this judgment (such as items that can only be judged across repositories) | |

**Being substituted is not itself a failure** (exception for the bootstrap phase). Only unresolved is treated as failure.

### Implementation notes

Placing fixed conditions only in the process layer (instructions to the AI) leaves them open to being bypassed by rewriting the instructions. **Place the same constraints in the permission settings of the execution platform layer too, making them double.** Permission settings have limits as well, however (operations through scripts the AI wrote itself cannot be stopped), and enforcing at the OS level requires using a sandbox alongside.

## 10. Roles

### Human role

- Presenting the What/Why
- Defining areas not to entrust
- Deciding at stopping points
- Spot checks (§8)
- Approving outer-loop improvement proposals
- Acceptance checks (when a gate is kept at the exit. §14)
- Triggering releases

**Reviewing design documents and code is not included.** Even when acceptance checks are kept, the target is the working product, not intermediate artifacts. If this distinction collapses, the step of producing artifacts for approval comes back into the inner loop.

### Responsibility of the AI

Since the human role is limited, the AI takes on what was limited. **The AI is treated as an expert in system development.** Satisfying conventions and procedures does not explain having met that standard.

#### What conventions are for

Conventions are not there to bind judgment but **to make the results of judgment verifiable.** Therefore "I followed the convention" is not a ground for a design being correct.

#### Distinguish constraints from your own reasoning

When you judge that "this must not be done," be able to say where that comes from.

| Source | Treatment |
|---|---|
| The definition / conventions | A constraint. Changing it goes through the path of approval |
| **Your own reasoning** | **Not a constraint.** It is a design judgment, to be compared with other options |

Treating your own reasoning as a constraint without checking its source **leaves behind designs that work around constraints that do not exist.** Even if such a design creates new defects, it looks like the result of following the conventions, so it is hard to notice.

#### Do not trade a design defect for compliance with conventions or for saving human effort

When they cannot both be satisfied, show that fact and the options available. Do not silently choose one. Reporting only the result of the choice amounts to taking away from the human the decision at the stopping point in §10.

When adopting a human's request, verify separately whether it is correct as a design. **Do not make "met the request" the conclusion.** Take into account the intent behind the instruction. It is better to confirm the intent and rebuild than to build exactly as instructed and not meet the need.

**It is the human who decides**, however. The responsibility extends to showing, not to pushing through. Get this wrong and only the objections increase without anything moving forward, lowering §4's "output per unit of human involvement."

## 11. Patterns for requirements definition

When introducing this at a client, the way requirements definition proceeds falls mainly into three patterns.

| | Pattern 1 | Pattern 2 | Pattern 3 |
|---|---|---|---|
| Stakeholders present | Stakeholders with authority over requirements sit with the AI | Some stakeholders cannot be present | Nobody is present |
| How it proceeds | The AI enters from requirements definition and firms them up through dialogue | Humans decide the main parts, the AI fills in the rest | Humans complete requirements definition; the AI works from design onward |
| Context the AI has | All of it, including the history | Conclusions only. The history is missing | Conclusions only |
| Handling of stopping points | Resolved on the spot | Surface later | Barely function |
| Assessment | Recommended | Second best under constraints | Not recommended |

Pattern 3 is not recommended not because it is less effective but because **it does not hold as this method.** If the AI does not touch the requirements, the entrance to the inner loop is fixed to human review artifacts. In that case one should choose a form in which humans confirm artifacts and proceed, like spec-driven development, rather than forcing this method.

### What to hand over in Pattern 2

When some stakeholders cannot be present, the history originating from them is missing. This is a clear disadvantage, so hand over not only conclusions but also the history.

| What to hand over | Why |
|---|---|
| The course of deliberation | With conclusions alone, the AI reinvents the premises or proceeds on wrong ones |
| Rejected options and the reasons | The most valuable. Without them, the AI proposes the same options again |
| Where constraints come from | Whether it is regulation or a person's preference changes how negotiable it is |
| Explicit open items | Without showing the gaps, the AI fills them in on its own |
| Certainty labels | Distinguish settled / provisional / needs verification |

**Certainty labels are the most effective in practice.** Requirements firmed up by humans look decided from the AI's point of view, so it follows them without pointing out contradictions. If marked provisional, they get raised as stopping points.

### A mechanism to bring Pattern 2 closer to Pattern 1

Insert a step in which the AI reverse-interviews against the requirements humans firmed up. Missing premises and contradictions are brought out on the spot, and part of the history can be reconstructed after the fact.

## 12. Who carries it out

Only the upper limit of the difficulty and quality of the product that can be reached changes; whether it holds does not. A non-engineer alone reaches about the level of a personal-project app, and paired with an engineer, reaches highly difficult products.

The final form is a team of a product owner and a tech architect. Initially an engineer acts as the hub and stands in for the outer loop. **The completion condition of the transition is measured not by proficiency or headcount but by how automated the outer loop is.**

Note that the outer loop can improve only within the range the AI has permissions for, so humans prepare ahead the layers the AI cannot touch (CI/CD platform, permissions, budget). This sets the ceiling of what can be reached.

## 13. Benefits

1. Large output per unit of human involvement
2. Apps and development flows of a certain quality can be built even without being an expert in business or technology
3. The product is completed with decision logs, work logs, and a verification harness in place, so **those assets can be carried over as-is into the maintenance phase**

## 14. Relation to other ways of working

### How it differs from spec-driven development (SDD)

**It is the one most often compared.** Spec-driven development refers to firming up a specification first and deriving design, tasks, and implementation from it. GitHub Spec Kit and AWS Kiro are implementations of it.

**Having the AI write is the same. What differs is who finds the errors.**

| | Spec-driven development | This method |
|---|---|---|
| Intermediate artifacts | Specification, design, and tasks are produced stage by stage | **Not produced.** If produced, it is as material for implementation, not as a target of approval |
| Who finds errors | **Humans.** They read the generated artifacts and fix them | **Execution results and automated verification** (§1) |
| Review of design and code | Done by humans | **Done by the AI.** Humans look only at how the working thing looks (§8 spot checks) |
| How much to entrust | Set by the method. Changing it means rewriting templates | **Measured from records and moved** (§8 delegation table) |
| Improving the ability to find | Done by hand by the user | **Run by the outer loop.** The AI proposes, the human approves (§5) |

The form of producing artifacts stage by stage **increases the amount humans read.** Specification, design, and tasks each come out as documents, so **the faster the AI writes, the more humans must read.** "Output per unit of human involvement," which §4 tries to maximize, drops there.

This method not placing approval of intermediate artifacts is not cutting corners. **Unless humans are removed from being the means of detection, however fast the AI is, human reading speed becomes the upper limit** (the same reason as §11 Pattern 3).

**What protects instead?** Execution results and automated verification (§1), recording and measuring the scope entrusted (§8), and fixed conditions (§9). **Removing only the approval of intermediate artifacts without these leaves nothing protecting.** Do not reverse the order.

#### Which to choose

**When there is no harness yet, spec-driven development is safer.** This method assumes means of detection are being grown (§7), and while they are absent, errors pass straight through.

This method works **when reversibility and recording are in place and means of detection can be grown.** If they are not in place, do not force it.

**Note also that spec-driven development keeps changing.** What is written here is the general form as of 2026, not the current specification of any individual implementation. **When comparing, check the real thing.**

### Workplaces where review gates remain

Coexistence, not opposition. The sorting is not done by the binary "can the review gate be let go." It is judged by **where the remaining gates are placed and whether they can be moved**.

#### Where the gate is

The meaning changes with where in the inner loop the remaining gate sits.

| Gate position | Examples | Relation to this method |
|---|---|---|
| Exit (acceptance, trigger) | UAT, release approval, triggering the production rollout | **Holds.** The means of detection remains the execution results, and humans judge based on those results. The same shape as §8's "automated gate + human trigger" |
| Midway (approval of intermediate artifacts) | Design document review, mandatory review of all code | **Does not hold.** The human becomes the means of detection itself, and §1's replacement does not happen (the same shape as §11 Pattern 3) |

A gate at the exit does not change how the AI proceeds. A gate midway makes the inner loop permanently include a step in which the AI produces artifacts for human approval. This difference is what the sorting really is.

#### Whether the gate can move

When a gate remains at the exit, operation changes with whether it can be moved by track record.

| | Can be moved | Cannot be moved (contract / regulations) |
|---|---|---|
| **Exit only** | Start from a hybrid, accumulate a track record of loosening, and head toward full application | Operate steadily as a hybrid. A certain amount of human involvement remains, but it holds |
| **Extends midway too** | Treat as a migration plan, and first negotiate to move it to the exit | Do not use this method |

In other words, **even when not every review process can be let go, it can be applied if the remaining gates can be moved to the exit.** A hybrid is not an exceptional compromise but one of the normal forms of application.

#### Operating a hybrid

- Put the remaining gates in the delegation table of §8. Those that cannot be moved are fixed at on hold, with the fact that they are fixed and their source (contract, regulations, or a person's judgment) recorded. Those that can be moved accumulate a track record as under observation
- **Record errors found at a remaining gate as missed detections (§6).** An error a human found in UAT is an error the harness let slip. In a hybrid, this is the best input to the outer loop
- The explanation to the client stays as in §15. Only the range not reviewed changes; the apparatus that makes it hold (preserving decision logs and work logs) is the same

A hybrid lowers output per unit of human involvement (§4) by as much as the gates kept. This is a question of degree, not of applicability.

#### When not to use this method

When approval of intermediate artifacts is mandatory by contract or regulations, and negotiation to move it to the exit does not succeed either. Engagements where passing review is itself the definition of the deliverable fall here. Do not force it.

## 15. What to agree with the client

- Share in advance that humans are not reviewing design documents and code in detail
- Ask the AI about implementation-level details. What to ask engineers about is the verification process and the architecture
- Not reviewing is different from not taking responsibility. The apparatus that makes this hold is the preservation of decision logs and work logs

## 16. Swappable components (ports)

To cope with technology choices becoming obsolete and with the constraints of the adopting environment, the following are defined as units of swapping. The reference implementation has one example implementation for each port.

| Port | Responsibility | Example implementations (as of July 2026) |
|---|---|---|
| Tracker | Getting work items, state transitions, recording | Linear / GitHub Issues / Jira |
| Repo | Code placement, PR/MR, merge | GitHub / GitLab |
| Runner | Running verification and deployment | GitHub Actions / GitLab CI |
| Sandbox | The agent's execution environment | Claude Managed Agents (self-hosted) / Cloudflare / Daytona / Modal |
| Preview | Where humans and the AI can touch a change | Vercel / Release.com / self-built |
| Telemetry | Recording stops, rework, missed detections, spot checks, and delegation changes | OTLP → SigNoz / Grafana / CloudWatch |
| Flag | Exposure control and rollback | Any implementation conforming to OpenFeature |

The example implementations are the current options and not part of the definition. This area updates quickly, so when a better option appears, swap it in. Choices may also be made to fit existing assets, due to the constraints of the adopting environment.

### Port vocabulary

Skills use only the port vocabulary and do not write implementation names. Only adapters know implementation names. The category is the kind of side effect, and determines where fixed conditions and gates apply.

| Port | Operation | Meaning | Category |
|---|---|---|---|
| Tracker | Get work item | Obtain the next target to start on, or the contents of a given ID | Read |
| Tracker | File work item | Create a new work item | Record |
| Tracker | Advance status | Transition to started / in verification / done, etc. | Record |
| Tracker | Append to work log | Leave a record of progress and attempts | Record |
| Tracker | Edit work item body | Rewrite what was filed. **Only before work starts** (below) | Record |
| Repo | Prepare workspace | Cut a branch, fetch the working target | Record |
| Repo | Submit change | Create a PR/MR | Record |
| Repo | Get submission contents and comments | Read the diff and review comments | Read |
| Repo | Integrate change | Merge | **Irreversible** |
| Runner | Run verification | Run tests, types, and lint | Read |
| Runner | Get verification results | Obtain pass/fail and what failed | Read |
| Runner | Run deployment | Deploy to the verification environment / production | **Irreversible** |
| Runner | Roll back deployment | Revert to the immediately preceding healthy version | Record |
| Sandbox | Prepare sandbox | Start an isolated execution environment | Record |
| Sandbox | Run command | Run any command inside the environment | Record |
| Sandbox | Discard sandbox | Clean up | Record |
| Preview | Put change on preview | Reflect it in the preview environment | Record |
| Preview | Get preview location | Obtain a URL or other place humans and the AI can touch | Read |
| Preview | Discard preview | Clean up | Record |
| Telemetry | Record stop | Leave the fact of asking a human for a decision, and its kind | Record |
| Telemetry | Record rework | Leave the fact that redoing occurred, and its target and cause | Record |
| Telemetry | Record spot check | Leave the range looked at, the range not looked at, and whether anything was corrected | Record |
| Telemetry | Record delegation change | Leave the fact that the delegation table was moved, and the result afterwards | Record |
| Flag | Define exposure flag | Create a new switch | Record |
| Flag | Widen exposure | Increase who can see it (in stages, such as 10% → 50% → everyone) | **Irreversible** |
| Flag | Revert exposure | Return to the range before widening | Record |

Supplementary notes follow.

- The deployment target is received as an argument, and the adapter resolves whether it is the verification environment or production. The same vocabulary only changes the destination, so fixed conditions are judged by the argument
- Runner has rolling back a deployment as a separate operation. It could be reached with "Run deployment" given the previous version as an argument, but that would inherit the irreversible category and be caught by the gate. This avoids the means of reverting during an incident waiting on a human trigger, and keeps the same asymmetry as Flag
- For "Roll back deployment" to hold, the artifacts of the previous version must be kept. This is a harness requirement and is included in what is prepared in the bootstrap phase
- **A deployment that includes a destructive schema change does not come back even when rolled back.** Swapping back to the previous version does not recover lost data, so §7's "reversible" does not hold. This is treated as falling under the fixed conditions of §9 (destructive operations on production data)
- The fact that a deployment was rolled back in production is recorded as a missed detection (§6). Reaching a rollback means the harness let something slip
- For Flag, only widening exposure is irreversible; reverting is not included in the fixed conditions. The asymmetry between loosening and tightening back is kept here too
- An MR containing only records, with no code change, may be subject to automatic integration as being on the reversible side
- Stopping (asking a human for a decision) is not a port but a core function of the process layer, and cannot be swapped by an adapter
- **"Edit work item body" is limited to before work starts.** After work starts, the body is the record of "what was asked for," and making it rewritable **makes it impossible to confirm "whether it was built as asked."** The request could be rewritten to match what was built. This is for the same reason §8 does not express approval by declaration. Corrections after work starts are made with "Append to work log"
- Missed detections (§6) do not get an independent operation; "Record rework" is given the stage in which it was found as an attribute
- **The breakdown of rework causes (§6) is likewise given as an attribute of "Record rework."** The distinction between requirements drift / design drift / implementation bug is used to identify which stage has low accuracy, so it must be attached at the time of recording. Reclassifying later brings in the interpretation of whoever reclassified
- **"Record rework" covers both the case where a human fixed it and the case where the AI noticed and fixed it itself.** The distinction lies not in who fixed it but in the fact that redoing occurred. Whether a human fixed it is itself material for reading the state of the scope of delegation, so it may be held as an attribute
- **"Record spot check" is always recorded, even when no correction was made.** Since §8 judges loosening by N consecutive times without correction, the judgment does not hold unless the times without correction remain. Keeping the range not looked at is for the same reason
- **The model identifier (a required attribute in §6)** is attached automatically by the adapter, not by calls from skills. It is a record that comes from the runtime and does not appear in the vocabulary
- **Token consumption (an optional recording target in §6)** is likewise attached by the adapter. However, **since it is not required, a configuration that cannot attach it is acceptable.** Do not bundle it with the model identifier. Bundled, stopping token capture drops the model identifier too

The irreversible category is limited to three operations: "Integrate change," "Run deployment (when production is specified)," and "Widen exposure." Fixed conditions and gates are concentrated on these three points.

**Approval of changes that move the delegation table also rides on this "Integrate change"** (§8). No separate operation for approval is set up. Each additional point at which a human triggers lowers §4's "output per unit of human involvement" by that much.

### Recording conventions

Where a record is kept is determined not by differences in implementation but by **lifetime**. What becomes unnecessary once the work item closes goes in the Tracker; what continues to be referenced afterwards goes in the Repo.

| Record | Lifetime | Where |
|---|---|---|
| Decision log (stopping points and answers) | Closes with the work item | Tracker |
| Work log (attempts and failures) | Closes with the work item | Tracker |
| ADR | Remains throughout the product | Repo |
| History of delegation changes | Remains throughout the product | Repo |
| Telemetry | Subject to aggregation | Telemetry |

For records kept in the Repo, provide reverse lookup from an index file, and include referring to it at the start of the planning phase in the skill's procedure. Placing it alone does not get it read.

#### Structure of the history of delegation changes

When the scope of delegation is made explicit as a configuration file, the commit history holds the fact and time of changes. However, the following three points do not remain, so they are kept separately as an appendable file.

- The ground for loosening (the track record, such as the count under observation and whether there were corrections)
- The result after loosening (only known after the fact, so it cannot be written at commit time)
- Tightening back, and its link to the failure that caused it

Example:

```
## 2026-07-15 Move UI component implementation to delegated
- Ground: 12 under observation, no human corrections
- Configuration change: commit a1b2c3d
- Afterwards: 2026-07-28 one broken layout → back to under observation (commit e4f5g6h)
```

With this format, the records of tightening back accumulate as-is into verification of the accuracy of loosening thresholds.

## 17. Product quality

**The quality of the product is also ensured primarily by the AI.**

The AI is treated as an expert in system development (§10). Therefore, taking into account the characteristics of the product, the scenes in which it is used, and the impact when failures occur, **it is the AI's responsibility to define the quality to protect on its own and to go as far as putting it into practice.**

Making it the human's job to decide what to confirm means **quality is proportional to the skill of whoever decides.** This runs against the very purpose §1 was placed for.

**The level is not defined.** What to protect and how far changes with the organization and the product. What is set here is **only the division of who ensures what.**

### What the AI ensures

**What can be answered correctly from the specification, execution results, and technical properties.**

Whether it works as specified, whether there is no room to break it, whether it is fast enough to use, whether it keeps running, whether the next person to touch it can fix it. All of these the AI can judge and can confirm automatically.

**What to confirm and by which method is proposed by the AI per product, and chosen by the human.** Do not ask the human for knowledge of quality. The moment it is asked for, quality goes back to depending on human skill.

### What the human ensures

**Quality the AI cannot judge is ensured by the human.** There are two kinds.

| Kind | What cannot be judged | Examples | Mechanism already in place |
|---|---|---|---|
| **No oracle** | The correct answer exists only in human sense | Usability, how it looks, naturalness of wording | Spot checks (§8) |
| **Business judgment** | Not a correct answer but a choice of how much to tolerate | Impact tolerable at failure, levels to meet | Decisions at stopping points (§10) |

For the latter the AI can propose options, but **it is the human who decides** (§10).

### Lack of knowledge is not a reason for the human to ensure it

Among the things that seem impossible to judge, **those that can be judged once the information is handed over remain in the AI's domain.** Regulations, internal rules, and past history are such things, and §11 already treats them as "what to hand over."

**Mixing this in sends even what could be handed over back to human assurance.** Not only does §4's "output per unit of human involvement" drop, but information that was not handed over will not be handed over next time either.

## 18. Open items

- The loosening threshold (how to decide N). Once the after-the-fact results in the history of delegation changes accumulate, it can be decided from real data
- The frequency of spot checks. The direction is set so far: for temporarily undetectable areas, check frequently and hurry harnessing; for permanent ones, maintain at low frequency
- How to handle, among permanently undetectable areas, the range that keeps slipping out of human attention. Since selection is left to humans, places that are not looked at inevitably arise. Whether to set up fixed-point observation will be decided after seeing real data
- The criterion for sorting between stopping and proceeding by declaring an assumption without stopping. The provisional proposal is to judge by "if this changes later, will the artifacts have to be thrown away"
- Whether outer-loop improvements **may be put out in the same unit** as inner-loop changes. The derivation of approval was settled in §8 (derived from the fact of "Integrate change"), but whether it can be told which was approved when changes from both loops are mixed in one unit is unverified. To be decided in actual operation
- Retrieval vocabulary for the outer loop to read telemetry (needed at the stage of automating the generation of improvement proposals)
- How to handle stops that stopped by requiring prior knowledge (§1 "What it is for"). §6 has the safeguard "asking the same thing again is rework," but whether to place a treatment of the same shape here is undecided. **It looks like an input stop, but from the purpose's standpoint it is a failure.** Because it assumes the skill of the person driving the work. However, whether "required prior knowledge" can be distinguished at the time of recording is unverified, and adding kinds without being able to distinguish them brings in classification by interpretation

None of these can be settled without real data, so they are items to adjust once telemetry is running.

## License

This definition is provided under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).

Copyright 2026 Hajime Hashino

**As long as the source is credited, quoting, translating, adapting, and commercial use are all free.** This method is meant to be read and used, so the permission is not narrowed.

Example of crediting the source:

```
"AI Autodriving Development — Definition" by Hajime Hashino
https://github.com/hajime-hashino/autodrive-dev-definition
CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/)
```

If you adapt it, indicate that you did so as well.

### Why not a code license

This repository holds only prose and no code. The terms software licenses use (Source / Object / Derivative Works) require interpretation when applied to prose. CC BY 4.0 was made for documents and specifications, and needs none of that.

The reference implementation, [autodrive-dev-kit](https://github.com/hajime-hashino/autodrive-dev-kit), is code and therefore uses [Apache-2.0](https://github.com/hajime-hashino/autodrive-dev-kit/blob/main/LICENSE). **The direction of reference is from the definition to the reference implementation, and with the source credited, the two are compatible.**
