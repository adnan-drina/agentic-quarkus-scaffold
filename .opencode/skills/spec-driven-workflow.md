# Skill: Spec-driven workflow

How to execute the `/speckit.*` workflow in this project. Consult before
running any `/speckit` command.

## Command order

`/speckit.specify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement`.
The human reviews each artifact before the next command amplifies it — stop
after each command and let them review; do not chain commands yourself.

## Artifacts each phase must produce

A `/speckit` command is complete only when every artifact below exists.
Do not end your turn before they are all written; if unsure, list the
feature directory and verify before finishing.

- **specify**: `specs/<n>-<feature>/spec.md` and
  `checklists/requirements.md`.
- **plan**: `plan.md` **and** its supporting documents — `research.md`,
  `data-model.md`, `quickstart.md`, `contracts/` with the API contract.
  A filled `plan.md` alone is not a completed plan phase.
- **tasks**: `tasks.md` with ordered, individually verifiable items;
  tests are first-class tasks, not an afterthought.
- **implement**: source and tests per `tasks.md`, then `mvn -q test`
  green before reporting done.

## Writing specs (specify phase)

Describe user scenarios, business rules, constraints, and observable
behavior in plain language. Seed data and API paths given in a brief are
the contract — copy them exactly; never round, substitute, or invent
values. Do not choose the tech stack or write implementation detail in a
spec — that belongs to the plan, which is steered by the constitution
and the other skills in `.opencode/skills/`.

## Completion report

End every `/speckit` command with a short completion report listing the
artifacts written (paths) and the recommended next command.
