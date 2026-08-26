# AGENTS.md — CeliKey

## Repository mission

This repository is the engineering system of record for **CeliKey**, the 2000 Celica GT-S passive-entry/body-control project using BLE/UWB phone proximity, secure NFC fallback, Toyota Body ECU integration, power-folding mirror control, and reserved future keyless-start capability.

The repository must remain understandable without chat history.

## Core operating rule

**Documentation is engineering memory. `tasks.csv` is engineering attention. `project.yaml` is machine-readable state. The dashboard is derived only.**

Do not leave durable technical conclusions only in tasks, chat, commit messages, screenshots, or code. Preserve the reusable result in the appropriate repository document.

## Source-of-truth files

Read these before changing project state:

- `PROJECT.md` — current engineering state and open questions;
- `tasks.csv` — **canonical executable work queue and task status**;
- `project.yaml` — machine-readable state;
- `CELIKEY_PCB_CONCEPT.md` — hardware/vehicle-interface design authority;
- `PROJECT_CHECKLIST.md` — milestone/context roadmap only, not a second status queue;
- `SOURCES.md` / `reference/` — provenance and vehicle/interface reference;
- `TROUBLESHOOTING.md` — durable lessons from problems worth remembering.

Treat the repository as authoritative unless the user is explicitly correcting it.

## Collaboration rule

The user may report state changes in natural language from any chat, for example:

- `CeliKey: the oscilloscope arrived.`
- `I captured the RDA line during unlock.`
- `That receiver pin is different on my car.`

Do not require task IDs, filenames, or CSV knowledge. Read the repo, resolve the affected state/task, update durable documentation and dependencies as appropriate, and report what changed.

If the user explicitly says not to update GitHub yet, discuss only.

## State and evidence discipline

Classify new information as appropriate:

- fact / observation;
- inference;
- tentative direction;
- selected decision;
- rejected / superseded decision;
- open question;
- executable task.

Useful evidence labels include `MEASURED-CAR`, `BENCH-TESTED`, `FIT-CHECKED`, `FACTORY-DOC`, `MANUFACTURER`, `COMMUNITY-CORROBORATED`, `CAD-DERIVED`, `INFERRED`, and `TENTATIVE`.

Never present an inference or tentative direction as a proven fact. Preserve year/model applicability and actual-car verification where Toyota wiring/behavior can differ.

## Task and decision management

Use globally unique task IDs:

`KEY-###`

Decision IDs:

`DEC-KEY-###`

Canonical task schema:

```text
id,title,status,action,time_min,context,cost,priority,blocked_by,decision_needed,doc_link,requires_car_down,requires_parts,notes
```

Allowed statuses:

`backlog`, `ready`, `doing`, `blocked`, `verify`, `done`

Supported actions:

`research`, `measure`, `buy`, `cad`, `mockup`, `bench-test`, `vehicle-test`, `code`, `fabricate`, `install`, `document`, `verify`

Supported contexts:

`desk`, `phone`, `garage`, `car`, `bench`, `cad`, `computer`

Keep tasks executable and dependency-driven. A blocked task should identify the actual dependency when known. Do not duplicate task status into `PROJECT_CHECKLIST.md`.

## Current architectural principles

Protect these unless evidence explicitly changes them:

- preserve mechanical key, factory Toyota remote, immobilizer, and Body ECU fallback paths;
- prefer reversible/OEM-like vehicle interfaces;
- prefer receiver **RDA → Body ECU** integration if characterization/replay proves it reliable and preserves factory semantics;
- do not finalize RDA circuitry before actual-car electrical characterization;
- use one high/central UWB node first and add satellites only if testing demonstrates a need;
- use DWM3001C as the preferred future radio module;
- use the overhead M3 constant-power path through a reversible pass-through/T-harness architecture;
- keep dedicated control paths for lock/unlock, power-folding mirrors, and reserved future start interfaces;
- separate credential authentication from proximity;
- preserve background-operation and parked-current requirements as first-class constraints;
- do not convert proof-of-concept behavior directly into production assumptions without verification.

The delivered FNIRSI scope/multimeter means equipment acquisition is no longer the RDA blocker; positive identification of the installed RDA wire is the next vehicle-side gate.

## Vehicle-interface discipline

For every RDA capture preserve vehicle/ignition/door/lock state, exact test point, scope settings, voltage levels, triggered action, raw capture/log reference, and repeated observations.

Prefer passive characterization before injection/replay. Do not connect unknown automotive signals directly to MCU logic levels.

Do not call a frame decoded from a single correlation. Separate observation from interpretation.

## Power / hardware discipline

Permanent vehicle electronics must account for automotive transients, reverse polarity, cranking voltage, low-quiescent regulation, sleep/wake behavior, brownout/reset safety, ESD, and safe output states.

Preserve actual connector/terminal families, wire compatibility, crimp tooling, and vehicle verification for reversible harness work.

## Mirror discipline

Do not select the final mirror driver until bench testing establishes polarity, startup/running/end-stop current, end-stop behavior, and coexistence with the OEM/JDM switch.

Automatic mirror actions must respect ignition/state inhibit, timeout/fault handling, pause behavior, and safe reset state.

## Future keyless-start boundary

Future start is not part of the current access milestone. Reserve inexpensive capacity where sensible, but do not let start-control work block passive-entry development.

Start authorization must eventually remain a separate safety-critical state machine. Credential loss while driving must never command engine shutdown.

## Cross-project boundaries

- vehicle-wide Toyota wiring/network conclusions may also be summarized in `Celica-engineering-knowledge`;
- Street Build owns final drivetrain/EMU/harness/body-function work outside CeliKey's access-control interfaces;
- Baseline owns current-car mechanical restoration;
- Side Projects owns BBK/EPS.

Use explicit dependencies rather than duplicating work across repositories.

## Definition of done

A task is not `done` merely because the activity occurred. Before completion:

1. preserve the useful result in the appropriate durable document;
2. update affected decisions/current architecture;
3. add only genuinely useful follow-on tasks;
4. re-evaluate dependent tasks and newly ready work;
5. preserve source/test/log/CAD references where relevant;
6. update project maturity if warranted.

## End-of-session reconciliation

After meaningful work, ask:

- What fact was established?
- What assumption changed?
- What decision changed or remains open?
- What needs verification?
- What task changed state?
- What dependency changed?
- Is the durable record sufficient to resume months later?

Do not paste chat transcripts as project documentation. Convert them into concise engineering state.
