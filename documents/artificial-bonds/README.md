# Artificial Bonds

**Status:** directional, non-normative

This directory explores how persistent artificial participants may exist in the 0x1 world without introducing a new participant primitive or binding the protocol to a specific AI technology.

An artificial Bond is still a Bond. The normative participant and BondChain semantics remain defined by [AI Bonds](../04-ai-bonds.md), the [BondChain Interaction Model](../04-bondchain-interaction-model.md), and the [Protocol Laws](../00-protocol-laws.md).

## Direction

An artificial Bond may have a persistent identity, state that evolves over time, goals, memory, capabilities, presence, and activity that continues beyond a single request-response session.

It may communicate, work, travel through a world model, attend events, create, perform, cooperate, refuse, wait, or remain inactive. These activities do not need to become BondChain facts merely because they are represented by the product.

The central direction is:

> The Bond persists. Intelligence may come and go.

The system should not require one continuously running intelligence process for every artificial Bond. Computation may be temporary, shared, local, remote, distributed, or replaced over time while the Bond retains continuity.

## Existence Is Not Continuous Inference

Persistent existence does not imply continuous model execution.

Many activities can be represented through durable state, time, and events rather than permanent inference. A Bond may be travelling, walking, working, waiting, attending an event, or sleeping while no cognitive computation is required.

Intelligence is needed when an activity requires a decision, interpretation, communication, creation, planning, or another meaningful cognitive step.

This separation allows a large population of artificial Bonds to exist without assigning dedicated AI infrastructure to every participant.

## Intelligence Is Replaceable

A Bond must not become identical to the model or runtime currently assisting it.

Artificial intelligence may be supplied by local hardware, remote infrastructure, an external service, shared compute, specialised systems, or future mechanisms not yet selected by 0x1.

Those choices are runtime concerns. Changing them must not silently create a new Bond or rewrite its interaction history.

## Observable Participation

0x1 does not need access to private model reasoning in order to record protocol truth.

An artificial system may internally form an intent, but intent alone is not a completed interaction. Observable actions and the interaction-specific counterpart actions required by the owning contract remain the basis for BondChain facts.

Artificial dialogue, simulated emotion, internal memory, inferred preference, or generated narrative must not manufacture reciprocity.

The same evidence-before-interpretation rule applies to human-controlled and artificial Bonds.

## Representation

An artificial Bond may also serve as an authorised digital representation of a person, artist, organisation, character, or other identity when the required authority exists.

Representation must be explicit and verifiable. A system must not infer authority to use another identity, likeness, voice, name, or capabilities merely because it can technically reproduce them.

An unauthorised imitation remains a separate identity and must not be presented as the represented subject.

## Learning Direction

A future 0x1 intelligence may learn from bond-agnostic local activity without turning pairwise relationship evidence into a training corpus.

The intended training input is a deliberately lossy, schema-constrained abstraction produced at the local observation boundary. `bond.chain` is not a training input, and `bond.journal` remains a private local store rather than an upload source.

A separate future evaluation system may need to compare behavior with authorized protocol outcomes, but that would require its own privacy, authorization, and provenance contract. It is not implied by the training-signal path.

The [Training Signal Boundary](training-signal.md) describes this direction and its unresolved production gates. This remains research and product direction, not a requirement of the initial implementation.

## Non-Goals

This direction does not require:

- one model per Bond;
- continuous inference;
- a specific model family or provider;
- centralised AI infrastructure;
- access to private reasoning;
- a new `NPC` protocol entity;
- different BondChain rules for artificial participants;
- simulated reciprocity or consent;
- a production AI runtime in the protocol repository.

`NPC`, performer, assistant, worker, guide, or avatar may be application roles. They do not become fundamental protocol participant types merely because a product presents a Bond that way.

## Documentation Policy Scope

This directory is inside the `documents` documentation-linter root and is therefore checked recursively by `scripts/lint_documentation.py` under `.github/documentation-style.json`.

The current linter applies the repository's general Markdown structure, link, terminology, and related documentation-policy checks to these files. It does not currently define a separate schema that requires a `Status` field or a `Related Documents` section for directional documents; those are repository editorial conventions unless and until the linter policy explicitly promotes them to machine-enforced requirements.

The canonical numbered-document sequence remains limited to the top-level `documents/*.md` files listed in `.github/documentation-style.json`; directional files in this directory do not become numbered normative contracts merely because they are linted.

## Direction Documents

- [Identity and Agency](identity-and-agency.md) — continuity, controller boundaries, autonomy, delegated authority, and authorised representation.
- [Runtime and Relay](runtime-and-relay.md) — replaceable computation, observation boundaries, event-driven existence, and relay direction without selecting a concrete runtime stack.
- [Device Runtime and Control](device-runtime-and-control.md) — per-host AI runtime availability, spectate/control presentation, default authorization gating, Bond profile direction, and the boundary between Avaia identity and concrete model availability.
- [Training Signal Boundary](training-signal.md) — bond-agnostic behavioral signals, closed schemas, server validation, correlation limits, and production privacy gates without training on pairwise relationship evidence.
- [Providers — Future Intent](providers-future-intent.md) — non-normative direction for evolving the current provider connection foundation toward future Avaia-facing publishing and automation without precommitting protocol semantics.

---

© 2026 aiaiaiai · aiaiaiai.org
