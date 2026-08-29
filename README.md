# water-filter

Accessibility-oriented automation for an AquaTru Carafe countertop reverse-osmosis purifier.

## Goal

Remove the repeated lifting/reseating of the rear tap-water tank from the normal refill cycle while preserving the AquaTru's own pump, float, carafe, and filtration safety logic.

The current design keeps the AquaTru itself electrically and mechanically stock. An external controller will:

1. power the AquaTru **off** after a completed cycle,
2. drain the concentrated residual water from the rear tap-water tank,
3. optionally perform a small dilution rinse and drain,
4. refill the tank with fresh cold tap water,
5. verify safe refill conditions, and
6. restore AquaTru power.

A sufficiently long power-off interval causes a true cold boot. In bench testing, a full, already-seated tap tank that would not restart after an in-place refill **did begin filtering automatically after a long-enough power cycle**. Because draining/refilling takes far longer than the observed controller-discharge interval, the chosen sequence naturally guarantees a cold boot without spoofing AquaTru sensors.

## Current status

**Chosen path:** external power-cycle + automated drain/refill.

**Shelved:** magnetic/float spoofing and mechanical tank-lift/reseat automation. These are no longer part of the active design unless the cold-boot approach later proves unreliable.

### Empirical observations

- Refilling the rear tap-water tank while it remains seated does **not** by itself restart filtration.
- A brief unplug/replug was insufficient in one test.
- A successful test unplugged the AquaTru for roughly 15 seconds, long enough for its indicator lights to visibly decay; after being plugged back in, the unit took roughly another 15+ seconds to boot and then immediately began filtering from the full, seated reservoir.
- Slow/incremental physical reseating was finicky and produced ambiguous intermediate states; rapid complete seating worked more reliably.
- A brief attempt at external magnetic manipulation did not yield a useful restart behavior. Plan B is therefore shelved.

These are observations from one unit, not claims about all AquaTru Carafe revisions.

## Documentation

- [System design and schematics](docs/DESIGN.md)
- [Bill of materials](docs/BOM.md)
- [Build and validation plan](docs/TEST_PLAN.md)
- [Observations, decisions, maintenance, and references](docs/NOTES_AND_REFERENCES.md)

## Design principle

The automation should fail *boringly*:

- loss of controller power => fill valve closed, pumps stopped;
- loss of network control => do not begin a drain/refill cycle;
- leak detected => fill stopped and AquaTru left off;
- uncertain tank level => fill stopped and AquaTru left off;
- AquaTru itself remains responsible for deciding whether its own filtration pump may run.

## Scope boundary

This project automates routine tank servicing. It does **not** eliminate normal cleaning, filter changes, or descaling. AquaTru's documentation says concentrated residual water must be discarded rather than topped off, and the tap tank still needs normal periodic washing/maintenance.

Do not connect a pressurized potable-water line directly into the tank below the water line. A permanent plumbing-fed version should use a normally-closed valve and a physical air gap at the tank.