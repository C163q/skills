---
name: writing-project-documentation
description: Use when writing, updating, reviewing, or consolidating software project documentation, or when code changes affect documented behavior, interfaces, configuration, or workflows. Also applies to stale guidance and current documentation mixed with development history.
---

# Writing Project Documentation

**Project-specific documentation requirements always take precedence over this skill.** Read applicable `AGENTS.md` files, documentation policies, and nearby conventions first. Follow the project's language, structure, naming, lifecycle, and reporting requirements; use this skill where compatible.

Write for a reader who has the project but not the conversation. Preserve information that helps them act, understand a constraint, or make a decision. Keep necessary documentation current while reducing facts maintained by hand in multiple places.

## Decide what belongs in documentation

Identify the reader's task and what they should be able to do or understand afterward. Distinguish instructions for doing something, reference facts, and explanations of why. Choose a structure suited to that purpose; do not impose a directory taxonomy or template.

State prerequisites that materially affect the task. Explain project-specific concepts without assuming familiarity with internal conventions or earlier discussions.

Also identify the document's lifecycle. Current guides describe the supported system; specifications state requirements; task records preserve conclusions and evidence for a particular baseline. An active specification can remain authoritative for its task even when dated. Age alone does not establish validity.

Before adding text, find its existing home and decide whether prose adds value:

| Information | Preferred home |
| --- | --- |
| Fields, enumerations, defaults, resource values | Types, schemas, configuration, or resources; generate reference material when useful |
| Local implementation mechanics | Clear code; nearby comments for non-obvious reasons, with focused tests where appropriate |
| Cross-module contracts, lifecycle rules, important invariants | A focused explanation or reference linked to relevant implementation and checks |
| Installation, operation, troubleshooting, extension steps | A task-oriented guide with necessary prerequisites and observable outcomes |
| Goals, accepted requirements, constraints, decision rationale | Design or specification documents; decision records when their history matters |

Source code is evidence of implemented behavior, not proof that behavior meets the requirements. Keep user-facing guidance usable without requiring readers to reconstruct a workflow from source.

In developer guides, connect responsibilities and workflows to verified code entry points, key types, and relevant checks so readers can explore and modify the implementation.

Maintain one authoritative full definition of a rule. Elsewhere, provide the short context needed by that reader and a precise link. Necessary summaries are useful; independently maintained competing definitions are not.

## Write the current meaning

Edit the existing explanation around its final meaning instead of appending a report of the change.

- State the behavior or requirement directly, including conditions, limits, and consequences that affect use.
- For procedures, include necessary prerequisites, actions, expected results, and relevant failure or recovery behavior.
- Distinguish implemented facts, accepted but unimplemented requirements, proposals, and unresolved questions. Make uncertainty explicit rather than completing gaps from assumption.
- Give sections descriptive headings and enough context to make sense when found through search.
- Preserve reasons and trade-offs that still affect decisions. Remove conversational attribution, abandoned suggestions, revision sequences, and completion announcements from current guidance.

For example, after verifying timeout and status behavior, replace:

> This round we removed timeout retries after user feedback. The focused test passed.

Into a statement of the verified behavior and its implications:

> The client does not automatically retry a timed-out submission. A timeout can occur after server acceptance. Check status using the operation ID before resubmitting; if no ID was received, reconcile acceptance with the server first.

Keep verification evidence in the relevant task record or delivery note. Create a separate record when the project requires it or it has a concrete follow-up purpose. Avoid creating a new historical document for every edit.

## Use understandable language

Use familiar, precise words and established project names consistently. Explain unfamiliar terms where readers need them, and connect project concepts to actual code identifiers. Use abbreviations when they help readers, establishing their meaning on first use. Introduce a new term only when existing terminology cannot adequately name the concept; define it explicitly.

Define concepts through their meaning, scope, conditions, and use. Describe required actions directly.

Add a corrective contrast only to address evidenced confusion: a user question that reveals the confusion, an observed misuse, or a documented incident. A merely plausible misunderstanding is not evidence. Do not invent alternative interpretations to rebut.

State verified restrictions, unsupported behavior, and failure conditions explicitly, using negative statements when they convey those facts directly. Compare real alternatives when the task calls for a comparison.

## Synchronize from the behavior change

For a code-related update, inspect what changed in behavior, contracts, configuration, compatibility, or workflows. A file list alone does not reveal all affected documentation.

1. Find the authoritative explanation. Search for affected concepts, old and new identifiers, removed options, and previous behavior. Follow relevant links and check entry-point summaries, examples, tables, and limitations, including outside the initially named page.
2. Update affected current documentation in the same change. Repair or remove stale summaries and references alongside the full definition. When moving or consolidating content, preserve unique constraints and repair incoming links.
3. If implementation conflicts with an accepted requirement, preserve the requirement and expose the discrepancy. Follow project reporting rules; do not silently redefine intended behavior to match a bug. If evidence is insufficient, identify what remains unverified.
4. Read the complete affected sections after editing. Ensure the explanation is coherent and no contradictory old instruction remains elsewhere in the relevant current documentation.

An internal refactor may require no prose change when documented behavior and guidance remain correct. Determine the impact rather than modifying documentation merely to show activity. Leave historical evidence tied to its original baseline; move still-relevant conclusions into current guidance when needed.

## Address repeated staleness

When a passage becomes outdated repeatedly, assess its maintenance model as well as its accuracy:

- Consolidate duplicated definitions and retain necessary linked summaries.
- Generate mechanically derivable reference facts from their source instead of copying them manually. Edit generated content through that source.
- Move local implementation explanations closer to the owning code when appropriate.
- Remove prose that no longer serves a reader, preserving useful information and navigation before deleting it.
- For necessary, repeatedly missed contracts, make the relationship to the owning implementation or check discoverable. Propose targeted automation when its benefit justifies the cost.

Keep improvements within the task's scope. This skill does not require a new generator, dependency map, CI system, or repository-wide cleanup.

## Verify the useful claims

Use checks suited to the changed content: links and paths, executable examples, configuration validation, generated-output comparisons, or tests covering a documented contract. Run commands only when appropriate to the environment and authorized scope. State verification limits accurately.

Before delivery, check:

- Can a reader understand the scope and act without the development conversation?
- Are unfamiliar terms explained and corrective contrasts supported by evidence of confusion?
- Are behavior, requirements, and uncertainty distinguishable, with necessary reasons preserved?
- Have affected definitions, summaries, examples, and links been reconciled?
- Do current guides describe the system directly, with task progress and verification history in their proper place?
- What evidence supports the changed claims, and what remains unchecked?

A recently edited file, a new review date, or a passing link checker does not establish semantic accuracy. Report what changed, the relevant verification, and unresolved discrepancies concisely; avoid claiming exhaustive freshness without evidence.
