# Avaia `pub_dress` Naming and Registration

**Status:** normative identity amendment  
**Scope:** owned Avaia public-address naming and registration-coupled creation  
**Supersedes:** the owned-AI-avatar address-shape and creation-flow rules in `04-identity.md` and `04-ai-bonds.md` wherever they conflict with this document

This document fixes the naming and initial creation contract for the AI Avatar (`Avaia`) that belongs to a Bond. It defines address shape, deterministic default derivation, registration coupling, collision behavior, migration boundaries, and rename behavior. It does not redefine BondChain, Interaction, Relationship, consent, reciprocity, or AI runtime authority.

## Address role

`pub_dress` is a public address used to identify either a human Bond or that Bond's owned Avaia.

```text
Bond {
    pub_dress: 0x0sky
    avaia: {
        pub_dress: 0skai
        owner: <reference to this Bond>
    }
}
```

An Avaia is an AI Bond, not another human Bond and not a rendering-only object. The Avaia record MUST retain an explicit reference to its owning human Bond. Address text is not ownership authority.

## Canonical address shapes

```text
Bond.pub_dress  = 0x{n}{bond_slug}
Avaia.pub_dress = {n}{avaia_slug}
```

`n` is the owning Bond's immutable lowercase hexadecimal discriminator.

There is **no literal `x` prefix** in an Avaia `pub_dress` under this amendment. The previous `x{n}{avaia_slug}` form is superseded.

The human Bond slug grammar remains unchanged: 2–32 Unicode scalar values from the canonical allowlist. An Avaia slug uses the same scalar allowlist, MUST end in lowercase ASCII `ai`, and has a 2–34 scalar range. The 34-scalar maximum is deliberate: default derivation from any valid 32-scalar human slug must remain representable after appending the mandatory two-scalar `ai` suffix.

For an Avaia owned by `0x0sky`:

```text
Bond:   0x0sky
Avaia:  0skai
```

The identity types remain distinguishable by canonical grammar and registry type: a human Bond begins with `0x`; an owned Avaia begins directly with its owner discriminator and has an `ai`-terminated slug. Neither textual pattern replaces the explicit identity type or owner reference stored by the identity layer.

## Mandatory `ai` suffix

Every current Avaia slug MUST end with the literal lowercase ASCII suffix `ai`.

```text
avaia_slug.ends_with("ai") == true
```

Examples:

```text
0skai      valid
0mirai     valid
0zai       valid
0aiaiaiai  valid
0sky       invalid: missing terminal ai suffix
```

The suffix is protocol-visible address content, not a decorative UI badge. Clients, Core-facing validation, API validation, persistence boundaries, and resolvers MUST agree on it.

## Default Avaia address derivation

For a human Bond address:

```text
0x{n}{bond_slug}
```

the canonical default Avaia address is:

```text
if bond_slug.length > 1
and bond_slug ends in a naming-rule vowel:
    default_stem = bond_slug without its final character
else:
    default_stem = bond_slug

default_avaia_slug = default_stem + "ai"
default_avaia_pub_dress = n + default_avaia_slug
```

The lowercase ASCII terminal-vowel set is:

```text
a e i o u y
```

`y` is deliberately included as a deterministic product naming rule, not as a linguistic claim.

Examples:

```text
Bond           default Avaia
0x0sky    ->   0skai
0x0mira   ->   0mirai
0x0ze     ->   0zai
0x0sk     ->   0skai
0xda-sha. ->   da-sha.ai
```

The punctuation case is normative:

```text
0xda-sha.

n         = d
bond_slug = a-sha.
final . is not a naming-rule vowel
-> a-sha. + ai
-> a-sha.ai
-> d + a-sha.ai

0xda-sha. -> da-sha.ai
```

`xda-sha.ai` is **not** canonical under this amendment because it retains the superseded literal `x` prefix.

The 2–34 Avaia slug range makes this derivation total for every valid human Bond slug: a 32-scalar non-vowel human slug becomes a 34-scalar Avaia slug, while a terminal-vowel slug becomes at most 33 scalars after replacement with `ai`.

## Registration-coupled creation

A successful new human Bond registration MUST create exactly one owned Avaia as part of the same owner-authorized registration outcome.

```text
human registration request
  -> validate human pub_dress
  -> derive default Avaia pub_dress
  -> validate both address claims
  -> atomically create human Bond identity + owned Avaia identity + owner reference
  -> registration succeeds
```

This boundary is identity state, not pairwise interaction truth:

```text
Avaia creation during registration != Interaction
Avaia creation during registration != BondChain
ownership                       != reciprocity
ownership                       != Relationship
```

No second party has consented because the registrant now owns an Avaia. The Avaia is an actual AI Bond identity with an explicit owner reference, while its runtime may independently be checking, downloading, preparing, available, unavailable, unsupported, or in error on a specific device.

### Native and provider-backed registration

Native credential registration and provider-backed registration MUST converge on the same identity outcome:

```text
registered human Bond
+ exactly one owned Avaia
+ explicit Avaia -> Bond owner reference
```

Authentication method and host do not change the naming or ownership contract.

### Atomicity and collisions

The human and Avaia address claims required by registration MUST be committed atomically.

```text
human claim available + Avaia claim available -> commit both
human claim unavailable                        -> commit neither
Avaia claim unavailable                        -> commit neither
persistence failure                            -> commit neither
```

A product MAY allow the owner to choose another valid, globally available Avaia slug before commit when the default collides. It MUST NOT silently change the discriminator, strip arbitrary content, or invent a random address merely to succeed.

Idempotent replay of a successful registration MUST resolve to the same human Bond and the same owned Avaia, never create another Avaia.

## Explicit Avaia address choice

Default derivation is deterministic but is not ownership authority or a permanent equality constraint with the human slug stem.

Where address management permits explicit owner choice, the owner MAY choose another globally available Avaia slug if it satisfies the Avaia grammar, including the `ai` suffix and owner-bound discriminator.

```text
aiaiai + ai -> aiaiaiai
0aiaiaiai
```

The system MUST NOT normalize a valid explicit choice merely because a shorter generated form exists.

## Pre-amendment migration boundary

A human Bond created before this amendment may legitimately exist without an owned Avaia because the previous contract required a separate creation flow.

That historical state MUST NOT be rewritten as though an Avaia existed at the earlier registration time. Implementations MUST NOT perform unauthenticated or bulk silent backfill that fabricates historical AI identity state.

A reconciliation path for an existing unpaired Bond MUST:

1. authenticate current control of the owning Bond;
2. derive or explicitly select a currently available Avaia address under this contract;
3. atomically create the Avaia identity and explicit owner reference now;
4. record the actual creation boundary rather than backdating it.

Historical BondChain records remain unchanged because reconciliation is identity management, not a past interaction.

## Bond slug rotation

Changing the human Bond slug MUST NOT silently rewrite the existing Avaia address.

```text
Bond:          0x0sky -> 0x0ze
current Avaia: 0skai
new suggestion: 0zai
```

After a successful Bond slug change, a client SHOULD offer an explicit owner-authorized choice to rotate the Avaia address. Keeping `0skai` while the owner Bond becomes `0x0ze` does not break ownership because the explicit owner reference remains authoritative.

## Separation of concerns

```text
address      = current pub_dress
identity     = stable Bond identity
ownership    = explicit Avaia -> Bond reference
derivation   = deterministic default naming convenience
```

Therefore:

```text
Avaia address similarity != Bond identity proof
Avaia address rotation   != new Avaia identity
Bond slug rotation       != automatic Avaia rotation
Avaia registration       != Avaia runtime availability
```

Historical authenticated records preserve the address/key binding actually used when that history was authorized.

## Invariants

1. `Bond.pub_dress` and `Avaia.pub_dress` are public addresses, not the underlying identity objects.
2. A human Bond slug is 2–32 canonical scalars; an Avaia slug is 2–34 canonical scalars and MUST end in lowercase ASCII `ai`.
3. A successful new human Bond registration creates exactly one owned Avaia in the same atomic registration outcome.
4. An owned Avaia MUST retain an explicit owner reference to its human Bond.
5. `Avaia.pub_dress` uses `{n}{avaia_slug}`, where `n` is the owner's immutable lowercase hexadecimal discriminator; there is no literal `x` prefix.
6. Default derivation always appends `ai`; if the human slug ends in `a`, `e`, `i`, `o`, `u`, or `y`, that final scalar is removed first.
7. `0x0sky` defaults to `0skai`; `0xda-sha.` defaults to `da-sha.ai`.
8. Default derivation MUST be representable for every valid human Bond slug, including the 32-scalar maximum.
9. Registration MUST NOT partially commit if either required address claim fails.
10. Idempotent registration replay MUST NOT create a second Avaia.
11. A valid explicit Avaia slug MAY differ from the generated stem but MUST preserve the grammar and `ai` suffix.
12. Bond slug rotation MUST NOT silently mutate the Avaia address.
13. Ownership survives address rotation through the explicit owner reference, not address reconstruction.
14. Pre-amendment unpaired Bonds require current owner-authorized reconciliation; implementations MUST NOT backdate Avaia existence.
15. Registration-time Avaia creation is identity management and MUST NOT be written as BondChain, reciprocity, consent, or Relationship truth.
16. Avaia identity existence MUST NOT be conflated with model/runtime availability on a host or device.

## Integration note

`documents/04-identity.md` and `documents/04-ai-bonds.md` still contain the previous `x{n}{slug}` address form and separate avatar-creation boundary. This normative amendment supersedes those statements where they conflict. Later consolidation should inline this contract into the owning identity and AI-Bond documents without changing these semantics.

---

© 2026 aiaiaiai · aiaiaiai.org
