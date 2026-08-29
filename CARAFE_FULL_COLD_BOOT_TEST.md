# Carafe-Full Cold-Boot Test

## Purpose

Determine how the AquaTru Carafe behaves when the rear tap-water reservoir has been serviced and refilled, but the front glass carafe is still full when the unit is cold-booted.

This test decides whether the automation may safely service the rear reservoir immediately after a completed filtration cycle and then leave the AquaTru powered on while waiting for the user to empty the front carafe.

## Question

Given:

- the AquaTru has completed a normal filtration cycle;
- the front glass carafe is still full;
- the AquaTru is powered off long enough to fully cold-boot on restart;
- the rear reservoir is drained and then refilled while remaining seated;

what happens when AquaTru power is restored?

The desired behavior is:

1. AquaTru boots normally.
2. It detects that the front carafe is full and does **not** run the purification pump.
3. It remains safely idle.
4. After the front carafe is emptied and correctly reseated, it begins a new filtration cycle using the already-refilled rear reservoir.

If this occurs reliably, the rear-reservoir automation can operate immediately after every completed batch without needing to know when the clean-water carafe has been emptied.

## Safety constraints

For this test, do not alter, bypass, spoof, or obstruct any AquaTru sensors.

Keep both genuine tanks correctly installed. Do not intentionally command the unit to run with an empty rear reservoir or a missing/misaligned front carafe.

This test uses only ordinary AquaTru operation plus a sufficiently long power interruption.

## Test setup

- AquaTru Carafe in normal working configuration
- Front glass carafe installed normally
- Rear tap-water reservoir installed and seated normally
- A timer or clock
- Optional notes/video to capture indicator behavior

No automation hardware is required for this test.

## Procedure

### Phase A — produce the initial state

1. Start with both tanks installed normally and enough tap water in the rear reservoir for a normal cycle.
2. Allow AquaTru to complete a normal filtration cycle.
3. Confirm that the front glass carafe is full, or at its normal automatic-stop level.
4. **Do not remove or empty the front carafe.**

Record the indicator state at the end of the cycle.

### Phase B — simulate automated rear-tank service

5. Unplug the AquaTru or otherwise completely remove mains power.
6. Leave power off while servicing the rear reservoir.
7. With the rear reservoir still seated if practical, drain/discard the concentrated residual water.
8. If testing the optional rinse concept, add a small quantity of fresh tap water and drain it again. Record whether a rinse was performed.
9. Refill the seated rear reservoir with fresh tap water to its normal full level.
10. Ensure that the power-off interval has been at least 30 seconds. Longer is fine; the eventual automation will naturally remain off throughout drain/refill service.

At this point the intended state is:

- front carafe: full and seated;
- rear reservoir: full and seated;
- AquaTru: fully powered off / cold;

### Phase C — cold boot with the front carafe still full

11. Restore AquaTru power.
12. Observe the unit for at least 60 seconds after its startup sequence completes.
13. Record:
   - startup-light sequence;
   - whether the purification pump starts;
   - whether any warning/maintenance indicator appears;
   - whether the machine settles into an idle state;
   - any unexpected noise, cycling, or repeated start attempts.

**Expected / desired result:** the machine recognizes the full front carafe and remains idle rather than attempting to filter.

### Phase D — empty the front carafe

14. If the AquaTru remained safely idle in Phase C, remove the front glass carafe normally.
15. Empty enough water to put it into its normal refill-needed state. For the cleanest test, empty it completely.
16. Reseat the glass carafe normally.
17. Observe the AquaTru for at least 60 seconds.
18. Record whether a new purification cycle begins automatically.

**Best-case result:** AquaTru begins filtering without any additional rear-reservoir removal, refill, power cycle, or intervention.

## Result classes

### Result A — ideal

- Cold boot with full front carafe: AquaTru remains idle.
- Empty/reseat front carafe: AquaTru begins filtering automatically.

**Design consequence:** rear-reservoir service can occur immediately after every completed filtration cycle. The AquaTru can then remain powered on and wait for ordinary human use of the front carafe.

### Result B — safe but requires another trigger

- Cold boot with full front carafe: AquaTru remains idle.
- Empty/reseat front carafe: AquaTru does not begin filtering.

**Design consequence:** determine whether a second cold boot after the front carafe is emptied is sufficient. Automation may need to leave AquaTru powered off until the clean-water carafe becomes available.

Do not assume the cause; record the actual machine state before changing the design.

### Result C — AquaTru attempts to filter despite full front carafe

- Pump starts, or machine otherwise behaves as though the carafe is available for more water.

**Design consequence:** stop the test if behavior appears unsafe. Do not use an always-on post-service design until the front-carafe interlock is understood. A conservative architecture would keep AquaTru powered off until the front carafe is known to be empty/available.

### Result D — fault / maintenance / unexpected state

Record the exact indicators and behavior. Do not repeatedly cycle the machine in an attempt to force the desired outcome. Preserve the observation as data and investigate separately.

## Repeatability

One successful trial is promising but not enough to make this behavior an automation assumption.

If Result A occurs, repeat the complete test at least three times on separate normal cycles before treating it as established behavior.

Suggested acceptance criterion:

- 3/3 consecutive trials produce Result A;
- no unexpected pump starts while the front carafe is full;
- no maintenance/error state appears;
- emptying/reseating the front carafe reliably triggers the next filtration cycle.

## Test record

Copy this block for each run:

```text
Run number:
Date/time:
Rear reservoir drained: yes / no
Optional rinse performed: yes / no
Approx. power-off duration:
Front carafe state at boot: full / other

Startup behavior:
Pump started while carafe full: yes / no
Idle after boot: yes / no
Warnings/maintenance indicators:

Front carafe emptied: yes / no
Front carafe reseated: yes / no
New filtration cycle began automatically: yes / no
Delay before filtration began:

Result class: A / B / C / D
Notes:
```

## Why this test matters

The currently preferred rear-reservoir sequence is:

1. power AquaTru off;
2. drain concentrated residual water;
3. optionally rinse and drain;
4. refill the rear reservoir;
5. restore AquaTru power.

The unresolved question is whether step 5 may always happen immediately after rear-reservoir service, even when the front carafe is still full.

A reliable Result A substantially simplifies the final system: the automation would not need to detect whether the front carafe has been emptied before servicing the rear reservoir or restoring AquaTru power.
