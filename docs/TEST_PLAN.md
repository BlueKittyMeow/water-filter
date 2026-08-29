# Build and validation plan

The project should advance in small, reversible stages. A stage is not complete until its failure modes have been tested deliberately.

## Stage 1 — reproduce the cold-boot result

Goal: establish that the restart behavior is repeatable before buying plumbing hardware.

Procedure:

1. Allow the AquaTru to reach its normal completed/refill state.
2. Leave the rear tap tank seated.
3. Empty the concentrated residual water and refill it manually in place.
4. Confirm that this alone does not restart filtration.
5. Unplug / switch off AquaTru for at least 30 seconds.
6. Restore power.
7. Record whether filtration starts automatically.
8. Repeat at least three normal cycles.

Pass criterion: 3/3 cold boots correctly recognize the full seated tank and behave normally.

Do not optimize the minimum power-off duration. The final automated drain/refill cycle will naturally keep the unit off for much longer than 30 seconds.

## Stage 2 — front-carafe interaction test

This is the most important unresolved workflow question.

Procedure:

1. Let a normal cycle finish with the glass carafe full.
2. Power AquaTru off.
3. Manually empty/refill the rear tap tank while leaving it seated.
4. Restore AquaTru power **while the front glass carafe is still full**.
5. Confirm no overflow and that the unit remains safely idle.
6. Remove, empty, and reseat the front carafe.
7. Observe whether filtering starts automatically.

Possible outcomes:

- **A:** it starts after the front carafe is emptied/reseated. Great; the automation can refill the rear tank immediately after each completed cycle.
- **B:** it does not start until another cold boot. Then the automation should refill the rear tank but leave AquaTru OFF until a human empties/reseats the front carafe or explicitly requests restart.

## Stage 3 — drain-only prototype

Goal: automate the physically difficult part without introducing pressurized water.

Hardware:

- smart plug / controlled outlet;
- drain pump;
- pickup tube;
- secured waste discharge tube;
- controller or temporary manual pump switch.

Procedure:

1. Complete a normal AquaTru cycle.
2. Command AquaTru power OFF.
3. Confirm smart plug reports OFF.
4. Start drain pump.
5. Measure time required to remove the concentrated residual water.
6. Let a peristaltic pump run briefly after pickup goes dry to establish a conservative drain time.
7. Stop drain pump.
8. Refill rear tank manually with fresh cold water.
9. Restore AquaTru power.
10. Verify normal filtration.

Record:

- starting residual volume;
- pump flow rate;
- drain duration;
- remaining water depth after drain;
- whether the pickup moves or interferes with the AquaTru float;
- any drips/siphoning after pump shutdown.

## Stage 4 — non-contact level sensing

Goal: prove high-level detection on the actual AquaTru tank before connecting a water valve.

1. Attach high-level sensor externally at the desired fill mark.
2. Fill tank slowly by hand.
3. Record sensor transition level for at least ten fills.
4. Empty/refill with tank in its normal seated position.
5. Confirm adjacent hardware and the AquaTru's own magnetic float do not cause false transitions.
6. If using a second overflow sensor, test it independently.

Pass criterion: no missed high-level detections and no false-high state during the test set.

The sensor should be treated as untrusted until this is demonstrated.

## Stage 5 — automatic refill from a non-pressurized source

Before connecting household water pressure, test the state machine with a small external jug and fill pump.

This validates:

- high-level stop logic;
- hard fill timeout;
- leak shutdown;
- controller state transitions;
- fill/drain mutual exclusion;
- fault/reset behavior.

Deliberately test:

- disconnect high-level sensor during fill;
- trigger leak sensor during fill;
- kill controller power during fill;
- kill Wi-Fi before a cycle begins;
- command a second cycle while one is already active.

Expected behavior in every case: water input stops or never begins, AquaTru remains OFF, and the controller enters a latched fault state requiring inspection/reset.

## Stage 6 — automatic pressurized refill

Only after Stage 5 passes.

Architecture:

```text
cold potable water
    ↓
manual shutoff
    ↓
normally-closed solenoid
    ↓
secured free outlet
    ↓
PHYSICAL AIR GAP
    ↓
AquaTru tap tank
```

Before leaving this system unattended:

- verify fill valve closes when 12 V power is removed;
- verify fill stops on normal high-level input;
- verify fill stops on timeout if high-level sensor is disabled;
- verify leak detector stops fill;
- verify supply-side manual shutoff is easy to reach;
- confirm the discharge tube cannot fall below the tank water line;
- check local plumbing/backflow requirements for the permanent water-supply branch.

## Stage 7 — optional dilution rinse

Do not add this until normal drain/refill is reliable.

Initial experiment:

1. power AquaTru off;
2. drain residual water;
3. add 250 mL fresh water;
4. drain;
5. inspect residual water / tank surfaces;
6. compare with 500 mL rinse;
7. choose smallest useful rinse volume, or disable rinse entirely if it adds no practical benefit.

The owner's manual requires concentrated waste to be discarded but does not appear to require a rinse after every cycle. Weekly washing/descaling remains separate.

## Proposed final automatic cycle

```text
WAIT
 ↓
completed-cycle condition
 ↓
confirm no leak + controller healthy
 ↓
AQUATRU OFF
 ↓
confirm smart plug OFF
 ↓
DRAIN
 ↓
[optional RINSE FILL → RINSE DRAIN]
 ↓
REFILL
 ↓
high-level reached
 ↓
FILL VALVE OFF
 ↓
verify leak dry + high level sane
 ↓
AQUATRU ON (or wait for front-carafe action, depending on Stage 2 result)
 ↓
observe startup
 ↓
WAIT
```

## Logging

For every automated service cycle, record at least:

- timestamp;
- service trigger source;
- AquaTru power before/after;
- drain duration;
- rinse enabled/disabled and duration/volume;
- refill duration;
- high-level sensor result;
- leak sensor state;
- smart-plug power readings before and after restart;
- final state (`OK` or fault code).

Useful fault codes:

- `POWER_OFF_UNCONFIRMED`
- `DRAIN_TIMEOUT`
- `FILL_TIMEOUT`
- `LEVEL_CONFLICT`
- `LEAK_DETECTED`
- `SMART_PLUG_UNREACHABLE`
- `STARTUP_NOT_OBSERVED`
- `MANUAL_ABORT`

## Maintenance test cadence

Even after commissioning:

- inspect tubing and fittings weekly at first;
- inspect peristaltic pump tube for fatigue;
- exercise the manual water shutoff periodically;
- test leak detector shutdown deliberately;
- test high-level timeout fallback;
- continue AquaTru's normal cleaning/filter/descaling schedule.