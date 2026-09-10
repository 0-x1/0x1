# Bond Location State

**Status:** draft v1  
**Companions:** [Protocol Laws](00-protocol-laws.md), [Architecture and Data Model](05-architecture-and-data-model.md), [Proximity, Relay, and Broadcast](11-proximity-relay-and-broadcast.md), [Map Architecture](12-map-architecture.md), [0x1 Core and Client Architecture](18-core-and-client-architecture.md)

## Purpose

A Bond may expose a current application location to an authenticated 0x1 runtime. That state is useful for placing the Bond in the live world, but it is not an Interaction, a BondChain, a Relationship, or a public `map.registry` presence by itself.

This document defines the authority and provenance of that state before a storage engine, Telegram adapter, or map renderer may use it.

## Model

The portable shape is:

```text
Bond.location = {
  coordinate: {
    longitude,
    latitude
  },
  mode: live | manual,
  updated_at
}
```

`coordinate` is WGS84 longitude/latitude. UTS #46 applies to public-address projection such as `pub_dress` labels; it MUST NOT be applied to geographic coordinates.

`updated_at` identifies when this location state was accepted. It does not prove that the Bond remained at that coordinate afterwards.

### `live`

`live` means the active coordinate came from an explicitly observed current-location input accepted for that Bond. In the Telegram host profile, this is the location payload Telegram sends only after the person explicitly chooses the current-position action.

A `live` coordinate is an observation at `updated_at`. It MUST NOT be described as continuously current after its observation time without a newer observation.

### `manual`

`manual` means an authorized operator explicitly selected the active coordinate. The coordinate is a declared application position, not an observation of physical location.

A `manual` coordinate MUST NOT be relabelled as device-observed location, even when it happens to equal the device coordinate.

Returning to `live` requires a new explicit current-location observation. Switching modes alone MUST NOT resurrect an older coordinate as a new observation.

## Authorization Roles

`user` and `admin` are application authorization roles. They are not Bond kinds, Relationship state, protocol authority classes, or public `.bond` identity fields.

The baseline capability profile is:

| Role | Current location | Manual location |
|---|---|---|
| `user` | allowed for its own Bond | denied |
| `admin` | allowed for its own Bond | allowed for its own Bond |

A deployment MAY decide which authenticated Bonds receive `admin`, but that policy MUST be resolved from authenticated Bond identity rather than unverified provider display data.

This contract does not grant an admin authority to modify another Bond. Cross-Bond administration requires a separate explicit capability contract.

## Telegram Input

Telegram is an adapter, not the owner of Bond location semantics.

For the baseline Telegram flow:

```text
Current position
-> Telegram current-location payload
-> authenticated Telegram/Bond binding
-> Bond.location { mode: live, coordinate, updated_at }

Set position (admin only)
-> explicitly selected Telegram map point
-> authenticated Telegram/Bond binding + admin authorization
-> Bond.location { mode: manual, coordinate, updated_at }
```

A location payload received without a pending location action MUST NOT mutate Bond location state.

A manual action MUST expire if its pending selection window expires; a later unrelated Telegram location payload MUST NOT be interpreted as that manual selection.

## Storage and Projection

Bond location is single-owner operational state associated one-to-one with the Bond. An implementation MAY normalize it into an adjacent table, but the application projection is `Bond.location`, not a second identity or a map-presence entity.

The stored state MUST preserve at least:

- active coordinate;
- `live` or `manual` mode;
- accepted/observed timestamp.

A renderer receives the active coordinate as presentation input. In `manual` mode it MUST NOT simultaneously present device geolocation as the Bond's map position.

Storage technology MUST NOT infer stronger authority from persistence. A row in PostgreSQL, SQLite, IndexedDB, or another store does not turn a manual point into an observed physical fact.

## Privacy Boundary

This location state is not automatically public.

Exact Bond coordinates MAY reach the operator only through an explicit authenticated location submission such as the Telegram current-location action or an authorized manual selection. They MUST NOT be inserted into public `map.registry`, anonymous activity counters, or pairwise disclosure merely because they are stored.

The existing privacy rule that anonymous activity/proximity contributions do not expose exact coordinates remains intact for those contracts. `Bond.location` is a distinct, explicit owner-submitted operational state with a different authority boundary.

Future recipient-addressed location disclosure MUST define who may receive the coordinate, freshness rules, offline/stale semantics, and whether a coarser derived H3 cell is sufficient before it is enabled.

## Cells

A map cell is derived geography, never the source of the Bond coordinate:

```text
Bond.location.coordinate -> H3 cell(resolution)
```

An implementation MAY derive cells for spatial indexing, aggregation, matching, or privacy. Changing cell resolution MUST NOT alter the stored coordinate or fabricate movement.

Two Bonds resolving to the same cell is proximity input only. It is not evidence that they met, interacted, consented, or completed a BondChain.

## Invariants

1. `Bond.location` is single-owner operational state, not bilateral truth.
2. The active location contains one coordinate, one provenance mode, and one accepted timestamp.
3. `live` identifies an explicit observed current-location input at a time; it is not perpetual physical truth.
4. `manual` identifies an authorized declared position and MUST NOT masquerade as device observation.
5. `user` and `admin` are application authorization roles, not Bond protocol types.
6. Baseline `admin` authority changes only its own Bond location.
7. Location mutation never creates an Interaction, BondChain, reciprocity, consent, or Relationship state.
8. Exact stored coordinates are not public map state by default.
9. H3 cells are derived from coordinates; cells do not replace coordinate truth.
10. UTS #46 canonicalization applies to address projection, not geographic coordinates.

## Related Documents

- [Protocol Laws](00-protocol-laws.md)
- [Architecture and Data Model](05-architecture-and-data-model.md)
- [Proximity, Relay, and Broadcast](11-proximity-relay-and-broadcast.md)
- [Map Architecture](12-map-architecture.md)
- [0x1 Core and Client Architecture](18-core-and-client-architecture.md)
