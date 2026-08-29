# Observations, decisions, maintenance, and references

## Device

Project target: AquaTru Carafe countertop reverse-osmosis purifier, rear tap-water tank / front glass carafe design.

AquaTru documents:

- rear tap-water tank capacity: 2.5 L;
- clean-water carafe capacity: 0.5 gal / 1.9 L;
- typical filtration time: 10–15 minutes;
- maximum power rating: 36 W;
- approximate idle draw: 4 W.

## Empirical test log

### Test 1 — refill in place

**Condition:** rear tank remained seated; concentrated residual water removed and tank refilled.

**Result:** filtration did not restart merely because the tank became full.

**Interpretation:** the controller retains a completed/refill state and does not continuously treat `tank now full` as a fresh-cycle command.

### Test 2 — brief power interruption

**Condition:** full seated rear tank; short power interruption.

**Result:** did not restart.

**Interpretation:** interruption was likely too short for full controller/power-rail discharge. No claim is made about exact electronics.

### Test 3 — long-enough cold boot

**Condition:** full seated rear tank. AquaTru unplugged for roughly 15 seconds, long enough for indicator lights to visibly fade/dim; after reconnecting power the unit took roughly another 15+ seconds to initialize.

**Result:** AquaTru immediately began filtering after startup.

**Interpretation:** a genuine cold boot causes the unit to evaluate current physical state and accept the already-full, already-seated rear tank without requiring physical removal/reinstallation.

This is the key enabling observation for the selected design.

### Test 4 — physical reseating

**Condition:** tank lifted/reseated slowly or incrementally versus quickly/fully.

**Result:** mixed / finicky. Slow seating could appear accepted before the tank was completely seated; quick complete seating behaved more reliably.

**Decision:** do not build around a mechanical tank-lifting actuator unless every external alternative fails.

### Test 5 — magnetic manipulation

**Condition:** limited external magnet experiments around the tank/float system.

**Result:** no useful restart behavior found.

**Decision:** magnetic spoofing is shelved. It is unnecessary if the cold-boot method remains reliable.

## Current design decision

The active service order is:

1. **power AquaTru OFF**;
2. drain concentrated residual water;
3. optionally perform a small fresh-water dilution rinse and drain;
4. refill with fresh cold tap water;
5. verify high-level and leak conditions;
6. **power AquaTru ON**.

Because steps 2–5 take much longer than the successful observed power-down interval, a separate `wait for capacitor discharge` step should not be necessary in normal operation. A minimum power-off timer can still exist as a defensive assertion.

## What AquaTru officially requires

The owner's manual says:

- use cold tap water;
- when `Empty & Refill Tap Tank` illuminates, remove the tap tank, discard the remaining water, refill with fresh cold water, and reinstall it;
- concentrated residual water must be discarded rather than topped off;
- failure to discard the concentrated residual can cause scale and damage;
- normal washing/maintenance remains necessary.

The project intentionally preserves the important chemical/hydraulic part of that instruction — **discard the concentrated residual and replace it with fresh water** — while replacing the inaccessible physical lifting/reseating gesture with an external cold boot.

This is an unsupported accessibility modification, not an AquaTru-approved procedure. It may affect warranty treatment if a failure is attributed to modification or abnormal operation.

## Rinse decision

A per-cycle rinse is optional.

AquaTru's current replacement-tank guidance says to always empty concentrated waste after each cycle and wash tanks weekly; this supports treating `drain completely + fresh refill` as the normal automated cycle and keeping any small dilution rinse as an experimental enhancement rather than a requirement.

Do not let an automated rinse become a substitute for periodic tank washing or descaling.

## Potable-water / backflow principle

A permanent plumbing-fed version should maintain physical separation between the potable supply outlet and the AquaTru tank water surface.

Use:

```text
potable cold-water branch
    ↓
manual shutoff
    ↓
normally-closed solenoid
    ↓
free discharge outlet
    ↓
AIR GAP
    ↓
AquaTru tank
```

Do not submerge the fill outlet. Check the plumbing/backflow rules that apply where the system is installed before making a permanent connection.

## Official AquaTru references

### Owner's manual

https://support.aquatruwater.com/hc/en-us/article_attachments/8597777625623

### Carafe getting-started page

https://support.aquatruwater.com/hc/en-us/articles/8597826151063-AquaTru-Carafe-Getting-Started

### Quick-start guide

https://support.aquatruwater.com/hc/en-us/article_attachments/8597823474967

### Tank capacity and filtration time

https://support.aquatruwater.com/hc/en-us/articles/7739351788567-What-is-the-AquaTru-Carafe-s-tank-capacity-and-filtration-time

### Power rating

https://support.aquatruwater.com/hc/en-us/articles/18204944451223-What-is-the-power-rating-for-the-AquaTru-Carafe

### Idle power draw

https://support.aquatruwater.com/hc/en-us/articles/18204983578519-What-is-the-idle-power-draw-of-the-AquaTru-Carafe

### Maintenance mode / slow filtration

https://support.aquatruwater.com/hc/en-us/articles/31514668976407--Maintenance-mode-AquaTru-Carafe

### Replacement tap-water tank

https://aquatru.com/products/aquatru-carafe-replacement-tap-tank

### Spare parts

https://aquatru.com/collections/spare-parts

### Tap-tank magnetic float spare

https://aquatru.com/products/carafe-tap-tank-floater-valve-kit

The magnetic-float reference is retained for documentation only; the magnetic spoofing plan is shelved.

## Candidate component references

### Shelly Plug US / local smart plug control

https://us.shelly.com/products/shelly-plug-us-gen4-black

https://us.shelly.com/blogs/documentation/shelly-plug-us

### DFRobot SEN0204 / XKC-Y25-T12V non-contact level sensor

https://www.dfrobot.com/product-1493.html

https://wiki.dfrobot.com/sen0204/

### Adafruit 12 V peristaltic pump

https://www.adafruit.com/product/1150

Adafruit explicitly notes that the included tubing is not FDA/USDA-rated; use it only for waste-drain prototyping or replace it with appropriate tubing if a pump is ever used on a potable-water path.

## General backflow reference

US EPA drinking-water distribution / cross-connection resources:

https://www.epa.gov/dwreginfo/drinking-water-distribution-system-tools-and-resources

## Next unresolved tests

1. Repeat cold-boot success for at least three normal cycles.
2. Test cold boot while the front clean-water carafe remains full, then empty/reseat the carafe and observe whether filtering begins automatically.
3. Measure actual concentrated-residual volume on this unit.
4. Prototype drain-pickup geometry with the tank seated.
5. Test a non-contact high-level sensor on the actual tank wall.
6. Only after those pass, automate refill.