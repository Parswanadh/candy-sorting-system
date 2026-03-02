# Procurement & Budget Analysis
## High-Speed Optical Candy Sorting Machine

**Review Date:** March 2, 2026
**Reviewer:** Procurement Lead
**Repository:** https://github.com/Parswanadh/candy-sorting-system

---

## Executive Summary

The candy sorting system project faces a **critical procurement crisis** that threatens the entire timeline. The original system architecture was designed around an FPGA-based approach using the ADNS-3050 optical mouse sensor, but the project currently **lacks both the FPGA (₹900) and a functional color sensor**.

### Critical Finding
**The color sensor gap is the single largest blocker** - without a working color detection method, NO subsystem can proceed:
- Color sensing team cannot validate detection algorithms
- ESP32 architecture cannot integrate sensor data
- Mechanical transport cannot be tuned for actual detection timing
- Servo actuation cannot implement sorting logic
- OpenPLC integration cannot receive color classification data
- Testing & validation cannot begin

### Budget Impact
- **Original Budget (with FPGA):** ~₹1,330
- **Revised Budget (no FPGA):** ~₹430-630
- **Immediate Critical Need:** ₹80-200 for color sensor

---

## What Was Planned (Original Architecture)

### Original Component Budget

| Component | Model | Planned Cost | Purpose | Status |
|-----------|-------|--------------|---------|--------|
| FPGA | GOWIN GW1N-9 (Tang Nano 9K) | ₹900 | Hardware-parallel processing | ❌ NOT PURCHASED |
| Color Sensor | ADNS-3050 (salvage) | ₹0-150 | Color detection + belt tracking | ❌ INCOMPATIBLE |
| MCU | ESP32 (NodeMCU ESP32-S) | ₹0 | OpenPLC control logic | ✅ HAVE |
| Servo Motors | SG90 × 3 | ₹150 | Sorting gates | ❌ NOT PURCHASED |
| DC Motors | Generic TT motor × 2 | ₹100 | Belt drive | ❌ NOT PURCHASED |
| RGB LEDs | Common cathode × 2 | ₹0 | Illumination | ✅ HAVE (wrong type) |
| IR Sensor | FC-51 or TCRT5000 | ₹30 | Gem detection trigger | ❌ NOT PURCHASED |
| **TOTAL** | | **~₹1,330** | | **~₹430 spent** |

### Original System Architecture
```
LAYER 1 - SENSING (FPGA Domain):
  - ADNS-3050 optical mouse sensor (30×30 pixel array, 6400fps)
  - RGB LED array strobes Red → Green → Blue sequentially
  - FPGA captures 3 intensity frames in 1.5ms (hardware parallel)
  - Belt position tracking via mouse motion registers

LAYER 2 - CONTROL (OpenPLC on ESP32):
  - ESP32 runs OpenPLC runtime (IEC 61131-3 standard)
  - Ladder Logic: Belt speed control, safety interlocks
  - Structured Text: Sorting decision logic
  - Timing: Calculate when to fire each servo gate

LAYER 3 - ACTUATION (Mechanical):
  - Two conveyor belts (singulation + transport)
  - 3× SG90 servo motors for diverter gates (3-stage cascade)
  - 7 collection bins
```

---

## Current Reality (As of March 2026)

### What the User Actually Has

| Component | Status | Notes |
|-----------|--------|-------|
| NodeMCU ESP32-S | ✅ Have | Board identified from photo |
| Generic Arduino UNO (clone) | ✅ Have | Currently used for testing |
| DC power supply module | ✅ Have | 7-10V input, 3.3V/5V output, USB port |
| RGB LEDs (common cathode) | ⚠️ Have | 2× Red, 2× Green, 2× Blue — DIFFUSED type (problem!) |
| 741 Op-amp IC | ⚠️ Have | Only 1× (need 3 for full circuit) |
| Resistors | ✅ Have | 47Ω ×5, 560Ω ×5, 4.7kΩ ×5 |
| Jumper wires | ✅ Have | |
| Breadboard | ✅ Have | |
| FCT3065-XY mouse sensor | ❌ Useless | CANNOT use for color (no pixel access) |
| Laptop | ✅ Have | Used as color test source |

### What Needs to Be Bought

| Item | Cost | Priority | Where to Buy | Delivery Time |
|------|------|----------|--------------|---------------|
| **TCS3200 color sensor module** | ₹80-120 | 🔴 CRITICAL | Amazon/Robu.in | 2-3 days |
| **OR TCS34725 module** | ₹150-200 | 🔴 CRITICAL | Amazon/Robu.in | 2-3 days |
| 2× more 741 op-amps OR LM358 dual op-amp | ₹20-30 | 🟡 Medium | Local electronics | Same day |
| Phototransistor (L14F1/SFH320) | ₹30-50 | 🟡 Medium | Local electronics | Same day |
| SG90 Servo motors × 3 | ₹150 | 🟡 Medium | Amazon/Local | 2-3 days |
| DC TT motors × 2 | ₹100 | 🟡 Medium | Amazon/Local | 2-3 days |
| IR sensor FC-51/TCRT5000 | ₹30 | 🟢 Low | Local electronics | Same day |
| ADNS-3050 (from old gaming mouse) | ₹0-150 | 🟢 Optional | Old mice / eBay | 5-7 days |

---

## Critical Gaps & Blockers

### 1. COLOR SENSOR GAP (🔴 CRITICAL BLOCKER)

**Problem:** No functional color detection method available

**Current Situation:**
- FCT3065-XY mouse sensor cannot read pixel data (only motion registers)
- RGB LED photovoltaic experiment FAILED (diffused lens, weak signal)
- No backup sensor available

**Impact:**
- ALL subsystems blocked
- Cannot validate any detection algorithms
- Cannot proceed with mechanical timing calculations
- Cannot implement sorting logic
- Cannot begin integration testing

**Solution Required:**
- **IMMEDIATE:** Purchase TCS3200 (₹80-120) or TCS34725 (₹150-200)
- Both proven in 100+ M&M/Skittle sorter projects
- TCS3200: 20-50ms detection, works with Arduino UNO
- TCS34725: 5-10ms detection, I2C interface, better resolution

### 2. FPGA GAP (🟡 MEDIUM PRIORITY)

**Problem:** Original architecture required FPGA for high-speed processing

**Impact:**
- Maximum throughput reduced from 1,428 gems/min to ~845 gems/min (with ESP32-only)
- Academic demonstration of "hardware-parallel processing" lost
- Must simplify timing budget

**Workaround:**
- Use ESP32-only processing (acceptable for course requirements)
- TCS34725's 5-10ms detection time fits within timing budget
- Throughput still meets minimum requirements (845 gems/min vs target 1,400)

**Decision Required:**
- Is FPGA essential for academic requirements (CO3: signal processing)?
- If YES: Purchase Tang Nano 9K (₹900) + ADNS-3050 from old mouse
- If NO: Proceed with ESP32 + TCS34725 approach

### 3. MECHANICAL COMPONENTS GAP (🟡 MEDIUM PRIORITY)

**Problem:** No servos, motors, or IR sensors for physical system

**Impact:**
- Cannot build physical sorting mechanism
- Cannot test mechanical transport design
- Cannot validate servo timing calculations

**Timeline:**
- Can be purchased after color sensor is validated
- Not immediately blocking for software/sensor development

---

## Revised Budget Analysis

### Option A: ESP32 + TCS3200 (Budget-Friendly)

| Component | Cost | Priority |
|-----------|------|----------|
| TCS3200 color sensor | ₹80-120 | 🔴 CRITICAL |
| SG90 servos × 3 | ₹150 | 🟡 MEDIUM |
| DC TT motors × 2 | ₹100 | 🟡 MEDIUM |
| IR sensor | ₹30 | 🟢 LOW |
| Miscellaneous (wires, etc.) | ₹50 | 🟢 LOW |
| **TOTAL** | **₹410-450** | |

**Pros:**
- Lowest cost option
- TCS3200 proven in many sorter projects
- Fits within tight student budget

**Cons:**
- Slower detection (20-50ms) = lower throughput
- Tight timing margins at high belt speeds
- More complex wiring (8 pins vs I2C's 2 pins)

### Option B: ESP32 + TCS34725 (Recommended)

| Component | Cost | Priority |
|-----------|------|----------|
| TCS34725 color sensor | ₹150-200 | 🔴 CRITICAL |
| SG90 servos × 3 | ₹150 | 🟡 MEDIUM |
| DC TT motors × 2 | ₹100 | 🟡 MEDIUM |
| IR sensor | ₹30 | 🟢 LOW |
| Miscellaneous (wires, etc.) | ₹50 | 🟢 LOW |
| **TOTAL** | **₹480-530** | |

**Pros:**
- Faster detection (5-10ms) = higher throughput
- Simple I2C interface (2 wires)
- 16-bit resolution, built-in IR filter
- Better timing margins
- Adafruit library support

**Cons:**
- Slightly higher cost (₹70-80 more)
- Still no FPGA for academic "hardware-parallel" demonstration

### Option C: Full FPGA System (Original Vision)

| Component | Cost | Priority |
|-----------|------|----------|
| Tang Nano 9K FPGA | ₹900 | 🟡 ACADEMIC |
| ADNS-3050 (from mouse) | ₹0-150 | 🟡 ACADEMIC |
| SG90 servos × 3 | ₹150 | 🟡 MEDIUM |
| DC TT motors × 2 | ₹100 | 🟡 MEDIUM |
| IR sensor | ₹30 | 🟢 LOW |
| Miscellaneous (wires, etc.) | ₹50 | 🟢 LOW |
| **TOTAL** | **₹1,230-1,380** | |

**Pros:**
- Full academic demonstration (hardware-parallel processing)
- Maximum throughput (1,428 gems/min)
- Best timing margins
- Original vision realized

**Cons:**
- Highest cost (₹900 more than Option A)
- ADNS-3050 sourcing uncertain (need old gaming mouse)
- Longer lead time (5-7 days for mouse sensor)

---

## Delivery Timeline Analysis

### Immediate (This Week)

| Action | Time | Cost | Blocker Resolution |
|--------|------|------|-------------------|
| Order TCS3200/TCS34725 online | Day 0 | ₹80-200 | 🔴 UNBLOCKS ALL |
| Order servos and motors | Day 0 | ₹250 | 🟡 Enables mechanical build |
| **Receive color sensor** | Day 2-3 | - | 🔴 CRITICAL MILESTONE |
| **Validate color detection** | Day 3-4 | - | 🔴 UNBLOCKS SUBSYSTEM TEAMS |

### Short Term (Week 2)

| Action | Time | Cost | Dependencies |
|--------|------|------|--------------|
| Receive servos & motors | Day 5-7 | - | Mechanical build |
| Build detection tunnel | Day 7-10 | ~₹100 (materials) | After sensor validation |
| Integrate with ESP32 | Day 10-14 | - | After detection working |

### Medium Term (Week 3-4)

| Action | Time | Cost | Dependencies |
|--------|------|------|--------------|
| Build mechanical system | Day 15-21 | - | After motors received |
| OpenPLC integration | Day 22-28 | - | After detection + mechanical |
| System testing | Day 29-35 | - | All subsystems integrated |

---

## Cheaper Alternatives Analysis

### Color Sensor Alternatives

| Option | Cost | Pros | Cons | Verdict |
|--------|------|------|------|---------|
| **TCS3200** | ₹80-120 | Proven, cheap | Slower, complex wiring | ✅ RECOMMENDED (budget) |
| **TCS34725** | ₹150-200 | Fast, simple I2C | More expensive | ✅ RECOMMENDED (performance) |
| Phototransistor DIY | ₹30-80 | Very cheap | Complex circuit, needs op-amps | ⚠️ RISKY (time) |
| ESP32-CAM | ₹679+ | Already have camera | Slow (25fps), thermal issues | ❌ NOT VIABLE |
| Phone camera | ₹0 | Free | Slow, complex API, thermal risk | ❌ NOT VIABLE |

### Mechanical Alternatives

| Component | Standard | Cheaper Alternative | Savings |
|-----------|----------|---------------------|---------|
| SG90 servos | ₹150 (3×) | Stepper motors | ₹50 but complex |
| DC TT motors | ₹100 (2×) | Salvaged from toys | ₹0 but uncertain quality |
| IR sensor | ₹30 | Photodiode + resistor | ₹10 but less reliable |

**Recommendation:** Stick with standard components (SG90, TT motors) - reliability is worth the small cost premium.

---

## What CAN Be Bought Locally

### Same-Day Availability (Local Electronics Shops)

| Component | Local Availability | Notes |
|-----------|-------------------|-------|
| Resistors (all values) | ✅ Yes | |
| Capacitors | ✅ Yes | |
| LEDs (all colors) | ✅ Yes | Including clear-lens RGB |
| Op-amps (741, LM358) | ✅ Yes | |
| Phototransistors | ✅ Yes | L14F1, SFH320 |
| BC547 transistors | ✅ Yes | |
| IR sensors (FC-51, TCRT5000) | ✅ Yes | |
| SG90 servos | ⚠️ Sometimes | Call ahead |
| DC motors | ⚠️ Sometimes | Generic types |
| Arduino UNO | ✅ Yes | |
| ESP32 boards | ⚠️ Sometimes | |

### Online-Only (2-3 Days Delivery)

| Component | Must Buy Online | Reason |
|-----------|-----------------|--------|
| TCS3200/TCS34725 | ✅ Yes | Specialized modules |
| Tang Nano 9K FPGA | ✅ Yes | Specialized board |
| ADNS-3050 | ✅ Yes | Requires sourcing old mouse |
| Specific servo models | ⚠️ Sometimes | Local may have alternatives |

---

## Action Items (Prioritized)

### 🔴 CRITICAL (This Week)

1. **ORDER COLOR SENSOR IMMEDIATELY**
   - Priority: URGENT
   - Cost: ₹80-200
   - Action: Order TCS3200 or TCS34725 TODAY
   - Expected delivery: 2-3 days
   - Blocks: ALL subsystems
   - Owner: Procurement Lead

2. **Validate sensor on arrival**
   - Priority: URGENT
   - Cost: ₹0
   - Action: Test with Arduino UNO immediately upon receipt
   - Success criteria: Reliable color detection for all 7 colors
   - Owner: Color Sensing Lead

3. **Communicate sensor decision to all teams**
   - Priority: HIGH
   - Cost: ₹0
   - Action: Update all subsystem teams on sensor selection
   - Impact: Allows teams to plan integration
   - Owner: Procurement Lead

### 🟡 HIGH PRIORITY (Week 2)

4. **Order mechanical components**
   - Priority: HIGH
   - Cost: ₹250 (servos × 3 + motors × 2 + IR sensor)
   - Action: Order after color sensor validated
   - Expected delivery: 2-3 days
   - Blocks: Mechanical build and testing
   - Owner: Procurement Lead

5. **Decide on FPGA requirement**
   - Priority: HIGH
   - Cost: ₹0-900 (decision-dependent)
   - Action: Consult with course instructor on CO3 requirements
   - Decision point: Is hardware-parallel processing essential?
   - Owner: Team Lead

6. **Build detection tunnel**
   - Priority: MEDIUM
   - Cost: ~₹100 (cardboard, acrylic)
   - Action: Fabricate light-isolation chamber
   - Dependencies: After color sensor validation
   - Owner: Mechanical Transport Lead

### 🟢 MEDIUM PRIORITY (Week 3-4)

7. **Source ADNS-3050 if FPGA needed**
   - Priority: MEDIUM (conditional)
   - Cost: ₹0-150
   - Action: Check old computer stores, eBay, salvage
   - Dependencies: FPGA decision
   - Owner: Procurement Lead

8. **Procure miscellaneous hardware**
   - Priority: LOW
   - Cost: ~₹50
   - Action: Wires, connectors, mounting hardware
   - Dependencies: Before final assembly
   - Owner: Procurement Lead

---

## Dependencies on Other Subsystems

### Questions for ALL Teammates

#### To Color Sensing Lead:
1. What are your pin requirements for the chosen sensor?
2. Do you need the ESP32's ADC1 pins exclusively?
3. What's your minimum acceptable detection time?
4. Can you work with TCS3200's 20-50ms, or do you need TCS34725's 5-10ms?
5. What calibration procedure do you need?
6. Do you need a specific detection tunnel design?

#### To ESP32 Architecture Lead:
1. Can you handle I2C communication for TCS34725?
2. What's your processing time budget for color data?
3. Do you need the FPGA for timing accuracy?
4. What's your memory constraint for color classification algorithms?
5. Can you integrate both sensor data and OpenPLC simultaneously?

#### To Mechanical Transport Lead:
1. What are your servo motor torque requirements?
2. Do you need specific DC motor RPM specs?
3. What's your belt tensioning mechanism?
4. Do you need materials for the detection tunnel?
5. What's your anti-bounce ceiling design?
6. Can you build with locally available materials?

#### To Servo Actuation Lead:
1. What's your servo timing requirement (actuation speed)?
2. Do you need SG90 specifically, or are alternatives acceptable?
3. What's your gate design (diverter mechanism)?
4. Do you need mounting hardware for servos?
5. Can you work with 3-stage cascade sorting?

#### To OpenPLC Integration Lead:
1. Can OpenPLC ESP2 run on ESP32 with sensor interrupts?
2. What's your communication protocol preference?
3. Do you need special I/O modules?
4. Can you handle the timing calculations for servo firing?
5. What's your safety interlock requirement?

#### To Testing & Validation Lead:
1. What test equipment do you need (multimeter, oscilloscope)?
2. Do you need calibration targets (color standards)?
3. What's your test procedure for sensor validation?
4. Do you need test candies (can we use actual M&Ms)?

#### To Academic Requirements Lead:
1. Is FPGA required for CO3 (signal processing demonstration)?
2. Can ESP32 + TCS34725 satisfy "hardware-parallel" requirements?
3. What documentation is needed for procurement choices?
4. Are there cost constraints for the project?
5. What's the deadline for complete system?

---

## Total Budget Needed (Revised)

### Minimum Viable System (No FPGA)

| Category | Item | Cost |
|----------|------|------|
| **Color Sensing** | TCS3200 module | ₹80-120 |
| | OR TCS34725 module | ₹150-200 |
| **Actuation** | SG90 servos × 3 | ₹150 |
| **Transport** | DC TT motors × 2 | ₹100 |
| **Detection** | IR sensor FC-51 | ₹30 |
| **Mechanical** | Cardboard, acrylic, fasteners | ~₹100 |
| **Electronics** | Jumper wires, connectors | ~₹50 |
| **TOTAL (TCS3200)** | | **₹510-630** |
| **TOTAL (TCS34725)** | | **₹580-680** |

### Full System (With FPGA)

| Category | Item | Cost |
|----------|------|------|
| **Processing** | Tang Nano 9K FPGA | ₹900 |
| **Sensing** | ADNS-3050 (from mouse) | ₹0-150 |
| **Actuation** | SG90 servos × 3 | ₹150 |
| **Transport** | DC TT motors × 2 | ₹100 |
| **Detection** | IR sensor FC-51 | ₹30 |
| **Mechanical** | Cardboard, acrylic, fasteners | ~₹100 |
| **Electronics** | Jumper wires, connectors | ~₹50 |
| **TOTAL** | | **₹1,330-1,480** |

---

## Recommendations

### Immediate Actions (Next 24 Hours)

1. **ORDER TCS34725 COLOR SENSOR** (₹150-200)
   - Faster than TCS3200 (5-10ms vs 20-50ms)
   - Simple I2C interface
   - Better timing margins
   - Adafruit library support

2. **ORDER SERVOS AND MOTORS** (₹250 total)
   - Don't wait for sensor validation
   - These components have long lead times
   - Can be returned if project cancelled

3. **CONSULT INSTRUCTOR ON FPGA REQUIREMENT**
   - Academic requirements may dictate approach
   - Better to know now than later
   - Could save ₹900 if not required

### Decision Framework

```
IF FPGA required for academic demonstration:
  → Purchase Tang Nano 9K (₹900)
  → Source ADNS-3050 from old mouse
  → Build full system as originally designed

ELSE FPGA optional:
  → Purchase TCS34725 (₹150-200)
  → Use ESP32-only processing
  → Still achieve 845+ gems/min throughput
  → Save ₹700-800
```

### Risk Mitigation

1. **Order BOTH sensor options** if budget allows
   - TCS3200 as backup (₹80)
   - Total insurance: ₹230-280
   - Reduces risk of sensor failure

2. **Source components locally where possible**
   - IR sensors, resistors, capacitors
   - Faster delivery if urgent
   - Support local businesses

3. **Keep receipts and packaging**
   - Easy returns if wrong parts
   - Documentation for academic requirements

---

## Conclusion

The procurement situation is **critical but solvable**. The color sensor gap is blocking all progress, but can be resolved within 3 days for ₹80-200. The FPGA decision is the main strategic choice - it represents a ₹700-900 cost difference but may be required for academic demonstration.

**Critical Path:**
1. Order color sensor TODAY (TCS34725 recommended)
2. Validate detection within 24 hours of receipt
3. Order remaining mechanical components
4. Decide on FPGA based on academic requirements
5. Complete procurement within 2 weeks

**Total Budget Range:** ₹510-630 (no FPGA) to ₹1,330-1,480 (with FPGA)

**Immediate Action Required:** Order TCS34725 or TCS3200 color sensor IMMEDIATELY to unblock all subsystems.

---

**Next Review:** After color sensor validation (Day 3-4)
**Contact:** Procurement Lead via team messaging
**Issue Tracker:** GitHub Issues - tag with `[Procurement]`
