# Provisional bill of materials

This BOM is intentionally staged. Do **not** buy everything at once; validate each stage before making the plumbing permanent.

## Stage 0 — already validated conceptually

- AquaTru Carafe countertop purifier
- stock rear tap-water tank
- stock glass clean-water carafe

Observed successful cold-boot behavior is documented in [NOTES_AND_REFERENCES.md](NOTES_AND_REFERENCES.md).

## Stage 1 — power-cycle + drain prototype

### 1. Controllable plug-in AC switch

**Candidate:** Shelly Plug US Gen4

- 120 V / 15 A, far above the AquaTru's documented 36 W requirement
- local automation, MQTT/webhooks/API support
- power monitoring useful for observing filtering vs idle behavior
- keeps DIY low-voltage electronics completely out of the AquaTru's mains circuit

Links:

- Product: https://us.shelly.com/products/shelly-plug-us-gen4-black
- Documentation: https://us.shelly.com/blogs/documentation/shelly-plug-us

A different plug-in smart outlet is acceptable if it supports reliable local control, state confirmation, and adequate electrical ratings.

### 2. Drain pump

**Prototype candidate:** Adafruit 12 V peristaltic liquid pump, Product 1150

- up to ~100 mL/min
- self-priming
- fluid touches only the replaceable pump tube
- suitable for proving the drain geometry, though a final build may use a higher-flow pump

Link: https://www.adafruit.com/product/1150

At ~100 mL/min, draining roughly 0.6 L would take about six minutes. A final 300–500 mL/min peristaltic pump would make the service cycle much faster.

**Important:** the tubing supplied with the Adafruit pump is explicitly not rated for food use. That is not important on a waste-only drain path, but any component used on the fresh-water fill path must be suitable for potable water.

### 3. Drain tubing

- tubing sized for the chosen pump
- enough length to reach from the tank pickup to a sink/drain
- rigid or weighted pickup end so the tube stays at the lowest practical tank point
- secure sink/drain clip so the discharge hose cannot jump free

Do not combine drain tubing with the fresh-water line.

### 4. Low-voltage pump driver

- logic-level MOSFET module rated comfortably above the pump's current draw
- flyback protection if the module does not already provide it
- separate fused 12 V supply recommended

## Stage 2 — automatic refill

### 5. High-level sensor

**Candidate:** DFRobot SEN0204 / XKC-Y25-T12V non-contact liquid level sensor

- mounts outside a non-metallic tank
- 5–24 V operating range
- adjustable sensitivity
- documented sensing thickness up to 13 mm
- IP67 probe

Links:

- Product: https://www.dfrobot.com/product-1493.html
- Wiki: https://wiki.dfrobot.com/sen0204/

This is a promising choice because it can monitor the AquaTru tank without adding another wetted component. It must be tested on the actual tank wall before relying on it.

A second sensor can be mounted slightly above the normal high-level sensor as an independent overflow/fault signal.

### 6. Leak / overflow detector(s)

Exact parts TBD; existing hydro/water-contact sensors may already cover this role.

Suggested locations:

- directly beneath the AquaTru / rear tank;
- beneath the fill plumbing/solenoid;
- optionally beneath the sink-side supply connection.

Any wet alarm must immediately close the fill valve and leave AquaTru power OFF.

### 7. Normally-closed fill valve

Requirements:

- 12 V DC preferred;
- **normally closed** when unpowered;
- rated for household cold-water pressure;
- wetted materials suitable for potable water;
- compatible with the chosen tubing/fittings;
- installed downstream of a manual shutoff.

A common RO-system-style 1/4-inch normally-closed inlet valve is mechanically convenient, but the exact valve should not be selected until the actual source plumbing and tubing size are known.

### 8. Fresh-water tubing

- potable-water-rated / RO tubing, likely 1/4-inch OD
- route outlet above the AquaTru tank's maximum fill level
- secure the outlet so it cannot fall into the water

The free outlet should preserve a true air gap above the tank.

### 9. Manual plumbing shutoff + tee/adaptor

Exact fitting depends on the cold-water source. For a permanent under-sink feed, use a dedicated manual shutoff on the branch serving the automation.

Permanent household-plumbing changes should be made in accordance with local plumbing rules; a plumber is appropriate for the mains-water branch if needed.

## Stage 3 — controller

### 10. Microcontroller

**Candidate:** ESP32 development board

Required jobs:

- read high-level, optional low-level, and leak sensors;
- control drain pump and fill valve through MOSFET drivers;
- command/read the smart plug over the local network;
- enforce state-machine timeouts and interlocks;
- expose a manual SERVICE NOW / RESET interface;
- log cycle times and fault events.

Home Assistant or another local orchestrator can be added later, but the water-safety interlocks should not depend on an internet service.

### 11. Power supply

Likely starting point:

- 12 V DC, ~3 A regulated supply for pump + valve;
- fused output;
- buck converter to 5 V if the controller is not independently USB powered.

Final sizing must follow the selected pump and valve current ratings.

### 12. Low-voltage output drivers

- one MOSFET channel for drain pump;
- one MOSFET channel for fill valve;
- optional additional channel if a separate fill/rinse pump is used in a non-plumbed prototype.

## Optional / useful additions

- physical SERVICE NOW pushbutton
- physical FAULT RESET pushbutton
- status LEDs or tiny display
- inline flow meter for logging and fill-volume plausibility checks
- second non-contact level sensor at the expected residual-water level
- optical sensor aimed at the AquaTru `Empty & Refill Tap Tank` indicator
- spare AquaTru rear tank/lid if a permanent tubing pass-through is eventually needed

AquaTru currently sells a replacement Carafe tap-water tank and float-related spare parts:

- Tap-water tank: https://aquatru.com/products/aquatru-carafe-replacement-tap-tank
- Spare parts collection: https://aquatru.com/collections/spare-parts

Prefer modifying a spare lid/tank rather than drilling the only original.

## Expected architecture cost strategy

The first useful prototype only needs:

1. controllable plug,
2. drain pump + tubing,
3. low-voltage supply/driver,
4. manual refill.

That prototype proves the hardest assumptions before adding automatic mains-water refill. The refill plumbing, sensors, controller, and optional rinse are separate increments.