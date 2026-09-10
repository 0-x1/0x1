# 0x1 Protocol Documentation

This directory contains the technical specification for the 0x1 identity layer, Bond and BondChain interaction model, artificial participants, creator offers and donations, business authority layer, geographic presence market, and pairwise settlement behavior.

The documentation is organized by authority boundary. Each document owns one architectural concern and links to adjacent contracts. All normative rules derive from the [Protocol Laws](00-protocol-laws.md).

## Product Identity

`0x1` is a protocol product within the `nilx.one` ecosystem. It is not a GitHub organization, company identity, or alias for `nilx.one`.

Its canonical repository is [`nilx-one/0x1`](https://github.com/nilx-one/0x1).

## Reading Order

The two-digit filename prefix encodes dependency tier. Begin at `00` and proceed through `19`. When documents share one dependency tier, the order in this canonical index and `.github/documentation-style.json` is authoritative.

## Foundation

- [Protocol Laws](00-protocol-laws.md) — normative root, authority laws, system-wide invariants, hierarchy, and change control.
- [Documentation Protocol](01-documentation-protocol.md) — document ownership, normative structure, layer boundaries, and change discipline.
- [Glossary](02-glossary.md) — canonical repository-wide vocabulary.
- [Protocol Overview](03-protocol-overview.md) — product thesis, authority model, and system invariants.
- [BondChain Interaction Model](04-bondchain-interaction-model.md) — canonical ontology for Bond, BondChain (`bch`), reciprocity, causal boundaries, terminal states, and relationship projection.
- [AI Bonds](04-ai-bonds.md) — artificial participants, autonomy vs authority, persistent state, work, digital-asset delivery boundaries, and future world presence without introducing a new participant primitive.
- [Artificial Bonds Direction](artificial-bonds/README.md) — non-normative product and research direction for persistent artificial participants, replaceable intelligence, observable participation, and authorised representation.
- [Identity](04-identity.md) — `pub_dress`, provider boundaries, registry stages, private identity, authenticated introduction, continuity, and recovery limits.
- [Avaia `pub_dress` Naming](04-avaia-pub-dress-naming.md) — mandatory terminal `ai`, deterministic default derivation such as `0x0sky -> x0skai`, explicit ownership reference, and Avaia address rotation behavior.
- [Architecture and Data Model](05-architecture-and-data-model.md) — bounded `bond.chain` encoding, `bond.journal`, public projections, and state ownership.
- [Cryptography and Wire Protocol](06-cryptography-and-wire-protocol.md) — key hierarchy, record envelopes, encryption, and fork safety.

## Pairwise Behavior

- [Bond and BondChain Lifecycle](07-bond-lifecycle.md) — interaction formation, reciprocal authorization, signatures, terminal boundaries, and synchronization.
- [Offers and Matrix Engine](08-offers-and-matrix-engine.md) — OFFER, flex, negotiation, ranking, veto, and exploration for the current human-controlled engine profile.
- [Atomic Multi-Bond Settlement](09-atomic-multi-bond-settlement.md) — atomic payment settlement across independent pairwise interaction edges without a global coordinator or materialized transaction graph.
- [Economics and Payments](10-economics-and-payments.md) — `level`, `bnd`, `exp`, pairwise payments, creator-sale value movement, donations, and digital-presence economics.
- [Creator Offers and Donations](10-creator-offers-and-donations.md) — creator as a contextual Bond role, authored offers, direct sales, donations, provider-agnostic settlement, and remote geographic creative presence.

## Discovery and Presence

- [Proximity, Relay, and Broadcast](11-proximity-relay-and-broadcast.md) — constant-rate discovery, relay behavior, map activity, and broadcast access.
- [Map Architecture](12-map-architecture.md) — cell activation, current business projections, future creator projection boundary, rendering, and client privacy. The public `map.registry` is not a live per-Bond location registry.
- [Bond Location State](12-bond-location-state.md) — owner-submitted Bond coordinate, `live`/`manual` provenance, application authorization roles, Telegram input, privacy boundary, and derived H3 cells.
- [Business Bonds and Presence](13-business-bonds-and-presence.md) — business-scoped Bond authority, creator-vs-business boundary, business BondChains, registry-backed physical presence, and auction-backed digital presence.
- [Digital Presence Auction](14-claim-auction.md) — funded bids, optional defense, premium allocation, automatic transfer, and cooldown.

## Operations and Delivery

- [Devices and Recovery](15-devices-and-recovery.md) — device states, revocation, rekeying, REC-REQ, and CONTINUE.
- [Security and Platform Notes](16-security-and-platform-notes.md) — threat model, iOS implementation guidance, and post-quantum migration.
- [Protocol Constants and Open Questions](17-protocol-constants-and-open-questions.md) — fixed invariants, draft parameters, unresolved decisions, creator commerce/map contracts, and autonomous AI authority profile.
- [0x1 Core and Client Architecture](18-core-and-client-architecture.md) — shared Rust product engine, WebAssembly and UniFFI boundaries, peer Web/iOS clients, MapLibre, and GPU fallback contracts.
- [Implementation Roadmap](18-implementation-roadmap.md) — staged delivery plan and validation gates.
- [0x1 Core Client Contract v0](19-core-client-contract.md) — exact Phase 0 envelopes, identifiers, compatibility, failures, canonical fixture bytes, and cross-runtime handshake.

## Status

**Version:** v1

**Identity layer:** Stage 1 provider-backed registration is implemented; Stage 2 self-signed identity and transparent registry behavior are specified as the target contract. Artificial identity bootstrap is not yet a production contract.

**BondChain model:** Bond is the authority-bearing participant and may be human-controlled or artificial; BondChain is one causally bounded bilateral interaction. The model is normative in [BondChain Interaction Model](04-bondchain-interaction-model.md). Downstream storage and lifecycle documents must follow it rather than redefine it.

**AI Bond model:** Normative at the ontology and authority-boundary level. AI Bond does not create a new participant or chain primitive. Autonomous signing, identity bootstrap, custody, compromise recovery, and AI-capable interaction schemas remain open before production.

**Creator model:** Normative at the role and semantic-boundary level. An ordinary Bond may act as a creator without becoming a BBond; creator offers are unilateral public state; sales and donations remain pairwise; settlement is provider-agnostic; creator map placement cannot claim physical location. Concrete offer, fulfillment, donation, and creator-projection schemas remain open before production.

**Atomic Multi-Bond Settlement:** Draft v1. Core authority, privacy, reveal, and timeout invariants are specified; exact transport timing and external settlement integration remain implementation concerns.

**Bond location:** Draft v1. A Bond may carry owner-submitted operational `location` state with an active WGS84 coordinate, `live` or `manual` provenance, and an accepted timestamp. It is not public `map.registry` state or bilateral truth by itself.

**Map and business layers:** Draft v2. Current business presence classes and auction allocation are specified; creator projections are defined only as a future distinct projection class. Cell activation, registry adapters, business authority, creator placement, timing, and key lifecycle remain open. Current `map.registry` does not expose live per-Bond movement or creator projections.

**Core and clients:** Proposed implementation baseline. 0x1 Core owns behavior shared across runtimes and is implemented in Rust. Web and native iOS are first-class peer clients using WebAssembly and UniFFI respectively. MapLibre remains the geographic renderer; custom Web graphics use WebGPU with WebGL2 fallback and no Canvas 2D path.

**Core client contract:** Normative pre-stable `0.1.0` boundary. Production interaction registries are empty. A synthetic fixture corpus defines deterministic native Rust, WebAssembly, and UniFFI parity without activating production messaging, consent, friendship, purchase, or AI interaction semantics.

## Normative Language

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are used in their conventional RFC sense.

The [Protocol Laws](00-protocol-laws.md) are the source of all normative authority. The complete writing contract is defined in the [Documentation Protocol](01-documentation-protocol.md). Where a document describes rationale rather than protocol behavior, it must remain distinguishable from normative requirements.
