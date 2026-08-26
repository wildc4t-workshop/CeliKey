# AGENTS.md — CeliKey

## Repository mission

This repository is the engineering system of record for **CeliKey**, the Celica passive-entry / body-control project. The project includes BLE/UWB phone proximity, NFC fallback, lock/unlock integration, power-folding mirror control, and provision for future keyless/push-button start.

The repository must remain understandable without any external task manager or chat history.

## Core operating rule

**Documentation is engineering memory. `tasks.csv` is engineering attention. Any dashboard is a derived view only.**

Do not put durable technical conclusions only in tasks, issue comments, commit messages, or chat summaries. If work creates knowledge that will matter later, write it into the appropriate repository document.

## Read before changing anything

Before making technical changes or updating project state, read the relevant current files, especially:

- `PROJECT.md`
- `CELIKEY_PCB_CONCEPT.md`
- `SOURCES.md`
- `PROJECT_CHECKLIST.md` if still applicable
- `tasks.csv` when present
- `project.yaml` when present
- any topic-specific document affected by the work

Treat the repository as authoritative over remembered conversation context when they conflict, unless the repository is explicitly being corrected.

## Collaboration rule

The user may report CeliKey state changes in natural language from any chat, for example:

- `CeliKey: the oscilloscope arrived.`
- `I captured the RDA line during unlock.`
- `That receiver pin is different on my car.`

Do not require the user to know task IDs, filenames, or CSV structure. Read the repository, resolve the affected task/state, update durable documentation and dependencies as appropriate, and report what changed.

If the user explicitly says not to update GitHub yet, discuss only.

## Engineering state model

Classify new information before recording it:

1. **Fact / observation** — measured, tested, factory-documented, manufacturer-documented, or otherwise directly supported.
2. **Inference** — technically reasoned but not yet directly verified.
3. **Tentative decision** — current preferred direction that may change with evidence.
4. **Selected decision** — architecture or implementation choice accepted as the working baseline.
5. **Rejected / superseded decision** — preserve rationale and replacement rather than deleting history.
6. **Open question** — unresolved information that blocks or influences design.
7. **Task** — an executable action that can create knowledge, make a decision possible, or advance implementation.

Never present an inference or tentative direction as a proven fact.

## Evidence language

When useful, identify the basis of a claim as one of:

- `MEASURED-CAR`
- `BENCH-TESTED`
- `FACTORY-DOC`
- `MANUFACTURER`
- `COMMUNITY-CORROBORATED`
- `INFERRED`
- `TENTATIVE`

Preserve source links, document identifiers, part numbers, test conditions, firmware versions, and dates when they affect reproducibility.

## Decision management

Use stable decision IDs with the prefix:

`DEC-KEY-###`

A decision record should capture:

- decision ID
- status: `tentative`, `selected`, `superseded`, or `rejected`
- date/checkpoint
- problem being resolved
- selected direction
- rationale
- evidence supporting it
- alternatives considered
- downstream implications
- verification still required
- superseding decision ID when applicable

Do not erase old architecture merely because a newer design is preferred. Mark it superseded and point to the replacement.

## Task management

Use globally unique CeliKey task IDs:

`KEY-###`

When `tasks.csv` exists, use this schema unless the program-wide schema is intentionally revised:

```text
id,title,status,action,time_min,context,cost,priority,blocked_by,decision_needed,doc_link,requires_car_down,requires_parts,notes
```

Allowed `status` values:

- `backlog`
- `ready`
- `doing`
- `blocked`
- `verify`
- `done`

Preferred `action` values:

- `research`
- `measure`
- `buy`
- `cad`
- `mockup`
- `bench-test`
- `vehicle-test`
- `code`
- `fabricate`
- `install`
- `document`

Preferred `context` values:

- `desk`
- `phone`
- `garage`
- `car`
- `bench`
- `cad`
- `computer`

Use integer minutes for `time_min`. Size tasks to approximately 15–60 minutes when practical. Break work expected to exceed roughly two hours into smaller independently useful tasks.

Use semicolon-separated task IDs in `blocked_by`, for example:

`KEY-014;KEY-021`

Cross-project dependencies may reference another globally unique task ID, for example:

`STREET-010` or `BASE-005`

A blocked task must identify the actual dependency whenever known. Do not use `blocked` merely to mean low priority.

## Definition of done

A task is not `done` merely because the physical or research activity occurred.

Before changing a task to `done`:

1. Record durable findings, measurements, files, or test results in the appropriate documentation.
2. Update any affected decision record.
3. Add newly exposed follow-on work to `tasks.csv`.
4. Re-evaluate dependent tasks and move them from `blocked` to `ready` when appropriate.
5. Update project phase/state if the work materially changed maturity.

The completed task should point through `doc_link` to the durable result whenever practical.

## Project phase

When `project.yaml` exists, use a maturity value such as:

`idea -> research -> concept -> selected -> design -> prototype -> verify -> build -> complete`

Phase describes subsystem maturity, not whether individual tasks are complete.

## CeliKey-specific engineering priorities

Protect these current architectural principles unless new evidence explicitly changes them:

- Preserve the factory Toyota key/remote/immobilizer as a fallback.
- Prefer reversible/OEM-like vehicle interfaces.
- Prefer integration through the factory receiver / Body ECU path when it preserves factory lock logic, chirp, and light-flash acknowledgement.
- Keep dedicated control paths for lock/unlock, power-folding mirrors, and future keyless/push-button start.
- Treat RDA signaling as unverified until characterized on the actual car.
- Preserve background-operation and parked-current requirements as first-class constraints.
- Do not collapse bench proof-of-concept behavior into a production vehicle assumption without verification.

## Key interfaces and dependencies

CeliKey may create or consume dependencies involving:

- Toyota receiver / Body ECU / RDA interface
- power-folding mirror driver
- ignition state
- future start authorization
- overhead DOME power architecture
- BLE/UWB/NFC credential behavior
- iOS background execution
- vehicle battery quiescent-current budget
- broader Celica electrical/body-network work

Current program boundaries are:

- Street Build owns final engine-controls/harness/cluster-body integration.
- Baseline owns current-car mechanical restoration and packaging work.
- Side Projects owns BBK and EPS.

When work exposes an interface owned by another Celica project, create an explicit cross-project task dependency rather than burying it in notes or reviving the retired subsystem-prefix architecture.

## End-of-session reconciliation

After a brainstorming, research, coding, bench-test, or vehicle-test session that materially advances the project, reconcile the repository before stopping:

- What new facts were established?
- What assumptions were invalidated?
- What decisions changed?
- What open questions remain?
- What new tasks were created?
- Which dependencies changed?
- Which blocked tasks are now ready?
- Did project maturity change?
- Are sources and provenance preserved?

Do not paste a conversation transcript as project documentation. Convert the useful outcome into concise engineering state.
