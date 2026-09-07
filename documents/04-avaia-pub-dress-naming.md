# Avaia `pub_dress` Naming and Registration

**Status:** normative identity amendment  
**Scope:** owned Avaia public-address naming and registration-coupled creation  
**Supersedes:** the owned-AI-avatar address-shape and creation-flow rules in `04-identity.md` and `04-ai-bonds.md` wherever they conflict with this document

This document fixes the naming and initial creation contract for the AI Avatar (`Avaia`) that belongs to a Bond. It defines address shape, the mandatory AI suffix, the default derivation algorithm, registration coupling, collision behavior, migration boundaries, and rename behavior. It does not redefine BondChain, Interaction, Relationship, consent, reciprocity, or AI runtime authority.

## Address role

`pub_dress` is a public address used to identify either a human Bond or that Bond's owned Avaia.

Conceptually:

```text
Bond {
    pub_dress: 0x0sky
    avaia: {
        pub_dress: 0skai
        owner: <reference to this Bond>
    }
}
```

An Avaia is an AI Bond, not another human Bond and not a rendering-only object. The Avaia record MUST retain an explicit reference to its owning human Bond. Address text is not the source of ownership truth.

## Canonical address shapes

```text
Bond.pub_dress  = 0x{n}{bond_slug}
Avaia.pub_dress = {n}{avaia_slug}
```

`n` is the owning Bond's immutable lowercase hexadecimal discriminator.

There is **no literal `x` prefix** in an Avaia `pub_dress` under this amendment. The previous `x{n}{avaia_slug}` form is superseded.

The existing Bond slug minimum remains two characters. This amendment does not change the human Bond grammar.

For an Avaia owned by `0x0sky`, the address discriminator is therefore `0`:

```text
Bond:   0x0sky
Avaia:  0skai
```

The two identity types remain distinguishable by their canonical grammar and registry type: a human Bond begins with the literal `0x`, while an owned Avaia begins directly with its owner discriminator and carries the mandatory terminal `ai` suffix in its slug. Neither textual pattern replaces the explicit identity type or owner reference stored by the identity layer.

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
0rai       valid only because the slug itself ends in ai
```

The suffix is protocol-visible address content, not a decorative UI badge. Clients, Core-facing validation, API validation, persistence boundaries, and resolvers MUST agree on this invariant.

## Default Avaia address derivation

When a Bond has a human address:

```text
0x{n}{bond_slug}
```

the canonical default Avaia address is derived from the Bond slug.

```text
if bond_slug.length > 1
and bond_slug ends in a naming-rule vowel:
    default_stem = bond_slug without its final character
else:
    default_stem = bond_slug

default_avaia_slug = default_stem + "ai"
default_avaia_pub_dress = n + default_avaia_slug
```

Because a valid Bond slug already contains at least two characters, a terminal naming-rule vowel is removed whenever present. The `> 1` guard prevents an empty stem if the underlying identity grammar changes in the future.

For this naming rule, the lowercase ASCII terminal-vowel set is:

```text
a e i o u y
```

`y` is deliberately included in this product naming rule. This is a deterministic naming convention, not a linguistic claim about every language or every occurrence of `y`.

Examples:

```text
Bond           default Avaia
0x0sky    ->   0skai
0x0mira   ->   0mirai
0x0ze     ->   0zai
0x0sk     ->   0skai
0xda-sha. ->   da-sha.ai
```

The `sky` case is normative:

```text
sky
-> remove terminal y
-> sk
-> append mandatory ai
-> skai

0x0sky -> 0skai
```

The punctuation case is also normative:

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

`xda-sha.ai` is **not** the canonical result under this amendment because it retains the superseded literal `x` prefix.

## Registration-coupled creation

A successful new human Bond registration MUST create exactly one owned Avaia as part of the same owner-authorized registration outcome.

Conceptually:

```text
human registration request
  -> validate human pub_dress
  -> derive default Avaia pub_dress
  -> validate both address claims
  -> atomically create human Bond identity + owned Avaia identity + owner reference
  -> registration succeeds
```

The registration boundary is identity state, not pairwise interaction truth.

Therefore:

```text
Avaia creation during registration != Interaction
Avaia creation during registration != BondChain
ownership                       != reciprocity
ownership                       != Relationship
```

No second party has consented merely because the human registrant now owns an Avaia. The created Avaia is an actual AI Bond identity with an explicit owner reference, but its runtime may still be unavailable, unsupported, downloading, preparing, or otherwise inactive on the current device.

### Native and provider-backed registration

Native credential registration and provider-backed registration MUST converge on the same identity outcome:

```text
registered human Bond
+ exactly one owned Avaia
+ explicit Avaia -> Bond owner reference
```

The authentication method or host does not change the Avaia naming or ownership contract.

### Atomicity and collisions

The human address and the Avaia address required by the registration outcome MUST be claimed atomically.

If either requested address cannot be claimed, the system MUST NOT persist a partial registration that creates only the human Bond or only the Avaia.

```text
human claim available + Avaia claim available -> commit both
human claim unavailable                        -> commit neither
Avaia claim unavailable                        -> commit neither
persistence failure                            -> commit neither
```

A product MAY allow the owner to choose another valid, globally available Avaia slug before committing registration when the default collides. It MUST NOT silently change the owner discriminator, strip arbitrary characters, or invent a random address merely to make the transaction succeed.

Idempotent replay of a successful registration MUST resolve to the same human Bond and the same owned Avaia rather than creating additional identities.

## Explicit Avaia address choice

The default derivation is deterministic, but it is not permanent ownership authority and need not remain equal to the human Bond slug stem after creation.

Where the registration or later address-management surface permits explicit owner choice, the owner MAY choose another globally available Avaia slug, provided all Avaia invariants remain satisfied, including the mandatory terminal `ai` suffix and the owner-bound discriminator.

For example:

```text
aiaiai + ai -> aiaiaiai
0aiaiaiai
```

The system MUST NOT strip, normalize, or collapse an explicitly entered valid Avaia slug merely because a shorter generated form exists.

## Pre-amendment migration boundary

A human Bond created before this amendment may exist without an owned Avaia because the previous contract required a separate explicit creation flow.

That historical state MUST NOT be rewritten as though an Avaia had existed at the earlier registration time.

Implementations MUST NOT perform an unauthenticated or bulk silent backfill that fabricates historical AI identity state. A migration or reconciliation path for an existing unpaired Bond MUST:

1. authenticate current control of the owning Bond;
2. derive or explicitly select a currently available Avaia address under this contract;
3. atomically create the Avaia identity and explicit owner reference now;
4. record its actual creation boundary rather than backdating it to the human Bond's original registration.

After reconciliation, the product projection may present the owned Avaia normally. Historical BondChain records remain unchanged because the reconciliation is identity management, not a past interaction.

## Bond slug rotation

Changing the human Bond slug does not silently rewrite the existing Avaia address.

Example:

```text
Bond:
0x0sky -> 0x0ze

current Avaia:
0skai

new default suggestion:
0zai
```

After a successful Bond slug change, the client SHOULD offer an explicit owner-authorized choice:

```text
Update Avaia pub_dress?
0skai -> 0zai
```

The owner may accept the suggested Avaia rotation or keep the existing Avaia address.

Keeping the old Avaia address does not break ownership:

```text
Bond.pub_dress:        0x0ze
Avaia.pub_dress:       0skai
Avaia.owner_reference: Bond identity for 0x0ze
```

Ownership MUST continue to resolve through the explicit Bond reference rather than by reconstructing the Bond slug from the Avaia address.

## Separation of concerns

The protocol distinguishes four things:

```text
address      = current pub_dress
identity     = stable Bond identity
ownership    = explicit Avaia -> Bond reference
derivation   = deterministic default naming convenience
```

Derivation MUST NOT become ownership authority.

Likewise:

```text
Avaia address similarity != Bond identity proof
Avaia address rotation   != new Avaia identity
Bond slug rotation       != automatic Avaia rotation
Avaia registration       != Avaia runtime availability
```

Historical authenticated records continue to preserve the address/key binding that was actually used when that history was authorized.

## Invariants

1. `Bond.pub_dress` and `Avaia.pub_dress` are public addresses, not the underlying identity objects.
2. The Bond slug minimum remains two characters under the current human identity grammar.
3. A successful new human Bond registration creates exactly one owned Avaia in the same atomic registration outcome.
4. An Avaia belonging to a Bond MUST retain an explicit owner reference to that Bond.
5. `Avaia.pub_dress` uses the `{n}{avaia_slug}` form, where `n` equals the owning Bond's immutable lowercase hexadecimal discriminator; there is no literal `x` prefix.
6. Every current `avaia_slug` MUST end with the literal lowercase ASCII suffix `ai`.
7. The default Avaia address derives from the current Bond slug and always appends `ai`.
8. When a Bond slug has more than one character and ends in `a`, `e`, `i`, `o`, `u`, or `y`, default derivation removes that final character before appending `ai`.
9. Consequently, `0x0sky` defaults to `0skai`, and `0xda-sha.` defaults to `da-sha.ai`.
10. Registration MUST NOT partially commit if the required human or Avaia address claim fails.
11. Idempotent registration replay MUST NOT create a second Avaia.
12. A valid explicitly selected Avaia slug MAY differ from the generated stem but MUST still end in `ai`.
13. Bond slug rotation MUST NOT silently mutate the Avaia address; clients SHOULD ask whether to rotate the Avaia address to the newly generated suggestion.
14. Keeping the previous Avaia address after Bond slug rotation MUST preserve ownership through the explicit owner reference.
15. Pre-amendment Bonds without an Avaia require current owner-authorized reconciliation; implementations MUST NOT backdate or fabricate an earlier Avaia existence.
16. Registration-time Avaia creation is identity management and MUST NOT be written as BondChain, reciprocity, consent, or Relationship truth.
17. Avaia identity existence MUST NOT be conflated with model/runtime availability on a specific host or device.

## Integration note

`documents/04-identity.md` and `documents/04-ai-bonds.md` still contain the previous `x{n}{slug}` address form and the previous separate avatar-creation boundary. This normative amendment supersedes those statements where they conflict. A later consolidation should inline this contract into the owning identity and AI-Bond documents without changing these semantics.

---

© 2026 aiaiaiai · aiaiaiai.org
