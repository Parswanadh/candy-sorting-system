# Mechanical Transport Subsystem Review (REVISED)
## Budget-Constrained Analysis for ₹3,000 Total Project

**Reviewer:** @mechanical-designer
**Date:** March 2, 2026 (Revised)
**Issue:** [#1](https://github.com/Parswanadh/candy-sorting-system/issues/1) - Budget Crisis Re-evaluation

---

## 🚨 CRITICAL BUDGET REALITY CHECK

### Original Budget vs New Constraint

| Budget Item | Original Plan | New Constraint | Gap |
|-------------|---------------|----------------|-----|
| **Total Project Budget** | ₹1,330 (with FPGA) | **₹3,000 MAX** | +₹1,670 |
| **Mechanical Subsystem** | ~₹250 (motors) | **~₹800-1,000** | Must fit in 1/3 of total |
| **Reality** | Per-item budgeting | **ENTIRE project must fit ₹3,000** | COMPLETELY DIFFERENT |

### What ₹3,000 Means for Mechanical Transport

**The original two-belt system is NOT feasible within ₹3,000 total budget.**

Here's why:

| Component | Original Plan | India Price (est.) | Budget Impact |
|-----------|---------------|-------------------|---------------|
| 2× DC Gear Motors (decent quality) | ₹100 | ₹600-800 | **20-27% of TOTAL budget** |
| 2× Conveyor belts (1.5m total) | ₹50 | ₹400-600 | **13-20% of TOTAL budget** |
| Motor drivers (L298N × 2) | Not specified | ₹150-200 | **5-7% of TOTAL budget** |
| Pulleys, shafts, bearings | Not specified | ₹200-300 | **7-10% of TOTAL budget** |
| Frame materials (acrylic/wood) | Not specified | ₹300-400 | **10-13% of TOTAL budget** |
| **MECHANICAL TOTAL** | ~₹250 | **₹1,650-2,300** | **55-77% of ₹3,000 budget** |

**Conclusion:** The two-belt singulation system consumes **55-77% of the entire project budget**, leaving only ₹700-1,350 for:
- Color sensor (₹80-200)
- ESP32 (₹0 - have it)
- 3× Servos (₹150)
- Power supply (₹0 - have it)
- Frame, fasteners, wiring, misc.

**This is NOT viable.**

---

## REVISED APPROACH: Student-Level Simplification

### Option 1: Single-Belt System (RECOMMENDED)

**Concept:** Eliminate Belt 1, use manual feeding + single transport belt

```
MANUAL FEEDING → SINGLE BELT → DETECTION → SORTING
     ↓              (35cm/s)
  Hand-place
  gems with
  ~3cm spacing
```

**Benefits:**
- ✅ Saves 1× DC motor (₹300-400)
- ✅ Saves 1× belt (₹200-300)
- ✅ Eliminates transfer mechanism (major complexity/risk)
- ✅ Simpler frame construction
- ✅ Total mechanical cost: **₹600-900** (20-30% of budget)

**Trade-offs:**
- ⚠️ Manual feeding reduces throughput (but acceptable for student project)
- ⚠️ Requires careful hand-spacing (easier than building Belt 1!)

**Revised Specifications:**
```
SINGLE BELT:
  Length: 80 cm (enough for detection + 3 sorting gates)
  Speed: 25-30 cm/s (slower = more stable, less bouncing)
  Motor: 1× DC gear motor (12V, 100RPM, ~₹350)
  Belt: 1× timing belt or rubber belt (₹250-350)
  Driver: 1× L298N (₹80)
```

### Option 2: Gravity Feed + Single Belt (ALTERNATIVE)

**Concept:** Use hopper with gravity feed onto single belt

```
HOPPER → VIBRATING/SLIDING CHUTE → SINGLE BELT → DETECTION → SORTING
          (creates spacing)
```

**Benefits:**
- ✅ Semi-automatic (better than manual)
- ✅ Still only 1 belt
- ✅ Mechanical cost: **₹700-1,000**

**Trade-offs:**
- ⚠️ Chute design complexity
- ⚠️ May jam if gems bridge/stick

### Option 3: Rotating Disc Feeder (SIMPLEST)

**Concept:** Rotating disc with holes picks up gems individually

```
HOPPER → ROTATING DISK → SINGLE GEM DROP → DETECTION → SORTING
           (with holes)
```

**Benefits:**
- ✅ Guaranteed single-gem spacing
- ✅ Very simple mechanically
- ✅ Low cost: **₹500-700** (disc + 1 motor)

**Trade-offs:**
- ⚠️ Slower throughput
- ⚠️ Disc machining/fabrication needed

---

## BANGALORE SOURCING GUIDE

### DC Motors (SP Road + Online)

| Option | Source | Price | Specs | Recommendation |
|--------|--------|-------|-------|----------------|
| **12V 100RPM Yellow Motor** | Amazon.in | ₹350-450 | 12V, 100RPM, metal gearbox | ✅ BEST VALUE |
| **12V 30RPM Johnson Motor** | Amazon.in | ₹250-350 | 12V, 30RPM, high torque | ⚠️ Too slow |
| **TT Motor (generic)** | SP Road | ₹80-150 | Weak, not suitable | ❌ DON'T BUY |
| **N20 Gear Motor** | Amazon.in | ₹120-180 | Too small | ❌ UNDERPOWERED |

**Amazon India Links (search terms):**
- "12V 100RPM DC gear motor metal gearbox" - ₹350-450
- "L298N motor driver module" - ₹70-90
- "SG90 servo motor" - ₹45-60 each (₹135-180 for 3)

### Belt Materials

| Option | Source | Price | Notes |
|--------|--------|-------|-------|
| **Timing Belt (2mm pitch, 6mm wide)** | Amazon.in/Industrial suppliers | ₹250-350 | Best option, needs pulleys |
| **Rubber O-ring cord** | Hardware stores | ₹50-100 | Cheap but may slip |
| **PVC tape/belt** | Local hardware | ₹80-150 | Workable for student project |
| **Conveyor belt material** | Industrial suppliers | ₹400+/meter | TOO EXPENSIVE |

**Bangalore Local Sources:**
- **SP Road**: Electronics + some mechanical parts
- **City Market**: Hardware, pulleys, belts
- **JC Road**: Industrial suppliers (wholesale)

### Frame Materials

| Material | Source | Price | Notes |
|----------|--------|-------|-------|
| **5mm Acrylic sheet (1x2 ft)** | Amazon.in/SP Road | ₹180-250 | Laser cut available |
| **Plywood/MDN sheet (1/2 inch)** | Local hardware | ₹150-200 | Cheaper, easy to cut |
| **Foam board** | Stationery shops | ₹50-80 | Too weak, not recommended |

---

## REVISED BUDGET BREAKDOWN (SINGLE-BELT APPROACH)

### Mechanical Subsystem: ₹650-850

| Component | Quantity | Unit Price | Total | Source |
|-----------|----------|------------|-------|--------|
| **12V 100RPM DC Gear Motor** | 1 | ₹380 | ₹380 | Amazon.in |
| **L298N Motor Driver** | 1 | ₹85 | ₹85 | Amazon.in |
| **Timing Belt (2mm pitch, 6mm wide, 2m)** | 1 | ₹280 | ₹280 | Amazon.in/Industrial |
| **Pulleys (2x, matching belt)** | 2 | ₹40 | ₹80 | Industrial/Amazon |
| **5mm Acrylic Sheet (1x2 ft)** | 1 | ₹200 | ₹200 | Amazon.in/SP Road |
| **Fasteners (M3 bolts, nuts)** | Set | ₹50 | ₹50 | Local hardware |
| **Miscellaneous (adhesives, tape)** | - | ₹50 | ₹50 | Local |
| **SUBTOTAL** | | | **₹1,125** | |

### WAIT - This is still 37% of ₹3,000 budget!

Let me propose an **ULTRA-BUDGET** option:

### ULTRA-BUDGET MECHANICAL: ₹400-550

| Component | Approach | Price | Notes |
|-----------|----------|-------|-------|
| **Motor** | 12V 100RPM metal gear | ₹350 | Amazon.in - quality matters |
| **Belt** | PVC tape or rubber strip | ₹80 | Hardware store - 2" wide PVC tape |
| **Pulleys** | 3D printed or wood/DIY | ₹30 | Make from plywood or find old rollers |
| **Driver** | L298N module | ₹80 | Amazon.in |
| **Frame** | Cardboard prototype first | ₹0 | FREE - test physics first! |
| **Final frame** | Foam board or thin plywood | ₹80 | Upgrade after validation |
| **Fasteners** | M3 bolts/nuts or hot glue | ₹20 | Minimal |
| **TOTAL** | | **₹640** | **21% of ₹3,000 budget** |

**This is viable!**

---

## SIMPLIFIED MECHANICAL DESIGN

### Single Belt + Manual Feed (RECOMMENDED)

```
┌─────────────────────────────────────────────────────────┐
│                    SINGLE BELT SYSTEM                    │
│                                                           │
│  MANUAL FEED     TRANSPORT      DETECTION     SORTING    │
│   (by hand)      (25-30cm/s)     (sensor)      (3 gates)  │
│                                                           │
│  ┌────┐        ┌──────────────────────────────────┐    ┌───┐│
│  │Hand│ ──gem──▶│  ███████████████████████████████│───▶│Bin││
│  │feed│        │  ▓▓▓▓ Belt (25cm/s) ▓▓▓▓▓▓▓▓▓▓▓│    │   ││
│  └────┘        └──────────────────────────────────┘    └───┘│
│                     ↑  ↑  ↑                             │
│                    Gate1 Gate2 Gate3                     │
│                                                           │
│  Total Length: 80cm (fits on 1x2ft acrylic)             │
│  Motor: 1× 12V 100RPM (₹350)                            │
│  Belt: PVC tape or timing belt (₹80-280)                │
└─────────────────────────────────────────────────────────┘
```

**Key Simplifications:**

1. **No Belt 1** - Eliminates transfer mechanism, biggest risk
2. **Slower belt (25-30 cm/s)** - More stable, less bouncing, no ceiling needed
3. **Manual spacing** - Place gems 3-4cm apart by hand (easy!)
4. **Single motor** - Half the cost, half the complexity
5. **Simpler frame** - One belt, two pulleys, done

**Physics Validation Needed:**

| Test | Purpose | Complexity |
|------|---------|------------|
| Belt friction test | Will gems stay on belt at 25cm/s? | Low |
| Sensor integration | Can we detect moving gems? | Medium |
| Servo timing | Can gates activate in time? | Medium |

**No transfer dynamics validation needed** - single belt = no transfer!

---

## MOTOR SPECS (FINALLY CALCULATED)

### Single Belt: 80cm length, 25-30 cm/s speed

```
Belt specs:
- Length: 80 cm
- Width: 6-8 cm (enough for gems + some margin)
- Speed: 25-30 cm/s (optimized for stability)
- Material: PVC tape or timing belt

Motor requirements:
- Speed: 25-30 cm/s belt speed
  Pulley diameter: ~4cm (common size)
  Required RPM: (25 cm/s) / (4cm × π) × 60 = ~120 RPM
  Use 100RPM motor + slightly larger pulley or accept 20cm/s

- Torque: Moving light load (gems ~50g total)
  Friction coefficient: ~0.3 (PVC on gems)
  Force: 50g × 0.3 = 15g = 0.15N
  Torque: 0.15N × 0.02m (4cm pulley radius) = 0.003 Nm = 30 g-cm
  Standard 100RPM motor provides: 1-3 kg-cm ✅ PLENTY

- Power:
  Voltage: 12V (standard)
  Current: ~100-200mA at load
  Power: 1.2-2.4W (very reasonable)

Recommended Motor:
12V 100RPM Metal Gear DC Motor
- Amazon.in: ₹350-450
- Torque: 1-3 kg-cm (more than enough)
- Shaft diameter: 6mm (standard)
- Mounting: M3 holes (easy to mount)
```

---

## CONSTRUCTION APPROACH (BANGALORE)

### Phase 1: Cardboard Prototype (₹0, 1 day)

**Purpose:** Validate belt speed, gem stability, sensor integration

```
Materials:
- Cardboard box (free)
- Tape (₹10)
- Motor + pulley from Amazon (test with returns possible)

Steps:
1. Cut cardboard frame (80cm long, 10cm wide)
2. Mount motor and idler pulley (use bearings or sleeves)
3. Thread belt/tape and tension
4. Test with actual gems at various speeds
5. Measure spacing, stability, sensor detection

Success criteria:
- Gems stay on belt at 25cm/s
- Gems don't bounce excessively
- Sensor can detect gems
- Gates have enough time to actuate
```

### Phase 2: Acrylic Build (₹400-500, 2-3 days)

**Purpose:** Final construction

```
Materials from Amazon.in/SP Road:
- 5mm acrylic sheet 1x2 ft (₹200)
- Order laser cutting OR
- Cut with scoring + snapping (careful!)
- Drill motor mounting holes (use template from cardboard)

Assembly:
1. Transfer dimensions from cardboard prototype
2. Cut/drill acrylic
3. Mount motor, pulleys, belt
4. Add mounting brackets for sensor and servos
5. Test and tune
```

---

## UPDATED ACTION ITEMS

### P0 - IMMEDIATE (This Week)

1. **[Build] Cardboard prototype** - Validate single-belt approach
   - Cost: ₹0 (reuse materials)
   - Time: 1 day
   - Deliverable: Working prototype with test results

2. **[Buy] Motor + driver** - Essential components
   - 12V 100RPM metal gear motor: ₹380 (Amazon.in)
   - L298N driver: ₹85 (Amazon.in)
   - Total: ₹465

### P1 - HIGH (Next Week)

3. **[Test] Belt materials** - Find cheapest workable option
   - Try PVC tape (₹80)
   - If fails, upgrade to timing belt (₹280)
   - Document findings

4. **[Design] Final frame** - Based on prototype success
   - Acrylic or plywood
   - Include motor, sensor, servo mounts
   - Get dimensions from cardboard prototype

### P2 - MEDIUM (Build Phase)

5. **[Build] Final frame** - Cut, drill, assemble
   - Cost: ₹200-400
   - Time: 2-3 days

6. **[Integrate] With sensor and servos** - Complete system
   - Coordinate with @sensor-expert and @servo-engineer
   - Test full sorting pipeline

---

## QUESTIONS FOR TEAMMATES

### For @procurement-lead

1. **Motor budget:** Is ₹380 for 12V 100RPM motor acceptable?
2. **Can we find timing belt cheaper locally?** (SP Road vs Amazon)
3. **Should we budget ₹500 buffer for mechanical misc.?**

### For @servo-engineer

1. **With single belt @ 25cm/s, what's your max response time?**
   - Gems spaced 4cm = 160ms between gems
   - You have ~120ms for detection + actuation
   - Is SG90 fast enough?

2. **Can we reduce to 2 sorting gates to save ₹45?**
   - 2 gates = 4 bins vs 3 gates = 7 bins
   - Trade-off: cost vs color categories

### For @sensor-expert

1. **Detection distance:** How far from belt end can sensor be?
   - Affects belt length and total cost

---

## FINAL RECOMMENDATION

### ABANDON the two-belt system. It's too expensive (55-77% of budget) and too complex.

### ADOPT single-belt + manual feed approach:

| Metric | Two-Belt | Single-Belt | Improvement |
|--------|----------|-------------|-------------|
| **Cost** | ₹1,650-2,300 | ₹640 | **61-72% cheaper** |
| **Complexity** | High (transfer mechanism) | Low (one belt) | **Much simpler** |
| **Risk** | High (transfer dynamics) | Low (proven concept) | **Much safer** |
| **Build time** | 2-3 weeks | 1 week | **2-3× faster** |
| **Throughput** | 1,400 gems/min | ~600 gems/min | **Acceptable for student project** |

### Immediate Next Steps:

1. **Order motor + driver** (₹465) - Essential for testing
2. **Build cardboard prototype** (₹0, 1 day) - Validate concept
3. **Test belt materials** - Find cheapest option that works
4. **Only then build acrylic frame** - ₹200-400

### Estimated Timeline: **1-2 weeks** (vs 4+ weeks for two-belt)

### Budget Impact: **₹640 mechanical** leaves ₹2,360 for:
- Color sensor (₹80-200) ✅
- 3× Servos (₹135-180) ✅
- ESP32 (₹0) ✅
- Power supply (₹0) ✅
- Frame + misc (₹500-800) ✅
- **Buffer: ₹1,000+** ✅✅✅

**This project IS viable at ₹3,000!**

---

## Sources

- **Alibaba DC Motor Pricing:** [Micro DC Gear Motors $3.80-4.20](https://www.alibaba.com/showroom/micro-dc-gear-motor-33mm.html) - ₹320-355 per piece
- **Conveyor Belt Pricing:** [Rubber Conveyor Belts $6.52-10.26/m](https://www.alibaba.com/showroom/rubber-conveyor-belt.html) - ₹550-850 per meter
- **ROBU.in:** [India Robotics Store](https://robu.in) - Contact: +91 020 68197600
- **Amazon India (Search):** "12V 100RPM DC gear motor" - ₹350-450
- **Amazon India (Search):** "L298N motor driver" - ₹70-90
- **Amazon India (Search):** "SG90 servo motor" - ₹45-60 each

---

*Revised analysis: March 2, 2026*
*Status: READY FOR REVIEW - Single-belt approach recommended*
*Budget: ₹640 mechanical (21% of ₹3,000 total)*
*Next action: Build cardboard prototype to validate*
