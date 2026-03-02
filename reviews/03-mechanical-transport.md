# Mechanical Transport Subsystem Review
## Two-Belt Singulation + Anti-Bounce Ceiling Analysis

**Reviewer:** @mechanical-designer
**Date:** 2026-03-02
**Issue:** [#1 - [Mechanical] Two-Belt Singulation Physics Validation](https://github.com/Parswanadh/candy-sorting-system/issues/1)

---

## Executive Summary

The mechanical transport subsystem is **conceptually sound but entirely unvalidated**. The two-belt singulation approach is theoretically sound for creating gem spacing, but critical physics assumptions remain untested:

**Critical Findings:**
- ✅ Speed ratio math (7× differential) is theoretically correct
- ⚠️ Belt-to-belt transfer dynamics are unanalyzed (major risk)
- ❌ Motor torque requirements completely uncalculated
- ❌ Anti-bounce ceiling effectiveness never tested
- ❌ Detection tunnel construction approach undefined

**Overall Assessment:** **CONCEPT STAGE** - Cannot proceed to fabrication without physics validation and motor specification.

---

## What Was Planned (Per Documentation)

### Two-Belt Singulation System

```
BELT 1 (Slow Feed):
  Length: 50 cm
  Speed: 5 cm/s (50 mm/s)
  Purpose: Natural gem separation via slow feed
  Expected spacing: ~5 cm between gems

BELT 2 (Fast Transport):
  Length: 100 cm
  Speed: 35 cm/s (350 mm/s) - 7× faster than Belt 1
  Purpose: Amplify spacing for detection/sorting
  Expected spacing: 5 cm × 7 = 35 cm (original design)
  Revised target: 25 mm spacing for throughput
```

**Physics Principle (from docs):**
> Speed differential creates automatic spacing. Gems naturally separate on Belt 1, then spacing is amplified when they transfer to faster Belt 2.

### Anti-Bounce Ceiling

```
Material: Transparent acrylic, 3mm thick
Height: 11 mm above belt surface
Purpose: Prevent gem tumbling at high belt speed (30-35 cm/s)
Gem diameter: 13 mm
Clearance: -2 mm (geometric constraint prevents standing)
Ends: 50 mm BEFORE detection zone
```

**Physics Principle (from docs):**
> 11mm gap means 13mm gems CANNOT stand upright. Must remain flat. Ends 50mm before sensor so gems exit ceiling stable and flat.

### Detection Tunnel

```
Material: Black matte cardboard (5-8% reflectivity)
Length: 100 mm
Slot width: 15 mm (entry and exit)
Purpose: Optical isolation from ambient light
```

---

## Current Reality

### Project Status: NO HARDWARE BUILT

After analyzing both documentation files:

| Component | Planned Status | Actual Status | Gap |
|-----------|---------------|---------------|-----|
| Belt 1 (slow feed) | Designed on paper | ❌ Not built | 100% |
| Belt 2 (fast transport) | Designed on paper | ❌ Not built | 100% |
| Transfer mechanism | Concept only | ❌ Not designed | 100% |
| Anti-bounce ceiling | Concept only | ❌ Not built | 100% |
| Detection tunnel | Concept only | ❌ Not built | 100% |
| DC motors | Spec'd as "generic TT motor" | ❌ Not purchased | 100% |
| Motor drivers | Not mentioned | ❌ Not designed | 100% |
| Power supply | Mentioned as 7-10V | ⚠️ Partial (DC module exists) | 80% |

### What the User Actually Has (from HANDOFF.md)

- ✅ NodeMCU ESP32-S (for control)
- ✅ Arduino UNO (for testing)
- ✅ DC power supply module (7-10V input, 3.3V/5V output)
- ✅ Breadboard, jumper wires
- ❌ **NO belts**
- ❌ **NO motors**
- ❌ **NO motor drivers (L298N, TB6612, etc.)**
- ❌ **NO mechanical frames**
- ❌ **NO acrylic or other construction materials**

---

## Critical Gaps Analysis

### Gap #1: Belt Transfer Dynamics (HIGH RISK)

**Problem:** Documentation assumes clean transfer from Belt 1 (5 cm/s) to Belt 2 (35 cm/s). No analysis of:

1. **Transfer point geometry:** How are belts positioned? Gap? Overlap? Angle?
2. **Gem behavior at transition:** 7× speed jump = massive acceleration
   - Acceleration: (35-5) cm/s over ~20mm transfer ≈ 1500 cm/s² = 1.5g
   - Will gem slip? Roll? Bounce? Jam?
3. **Bounce mitigation:** What if gem bounces at transfer? No mechanism documented.

**Impact:** Single-point failure for entire system. If transfer doesn't work, nothing works.

**Risk Level:** **CRITICAL** - Could render entire design unworkable

### Gap #2: Motor Torque Requirements (BLOCKING)

**Problem:** "Generic TT motor" specified with ZERO torque analysis.

**Required calculations (not done):**

```
BELT 1 (Slow Feed):
  Length: 50 cm
  Speed: 5 cm/s
  Belt material: Rubber (high friction)
  Load: ~50 gems × 0.5g each = 25g moving mass + belt mass
  Friction: Rubber-on-gems μ ≈ 0.6-0.8
  Required torque: UNKNOWN
  Startup torque: 2× running torque?

BELT 2 (Fast Transport):
  Length: 100 cm (2× longer)
  Speed: 35 cm/s (7× faster)
  Load: Heavier belt (longer) + gems
  Inertial load: 7× acceleration requirement
  Required torque: UNKNOWN
```

**Missing specs:**
- Motor RPM at operating voltage
- Torque curve (stall torque, running torque)
- Gear ratio needed (if any)
- Power consumption at max load
- Voltage regulation requirements

**Impact:** Cannot purchase motors without specs. Risk of underpowered motors (stalling) or oversized (waste money/budget).

**Risk Level:** **BLOCKING** - Must complete before @procurement-lead can buy components

### Gap #3: Anti-Bounce Ceiling Effectiveness (UNTESTED)

**Problem:** 11mm ceiling height is theoretical. Never tested with actual gems.

**Unanswered questions:**
1. Does 11mm actually prevent tumbling at 35 cm/s?
2. What about gem-to-gem collisions under ceiling?
3. Will gems bridge across ceiling (stick to acrylic via static)?
4. Heat buildup in enclosed space?
5. How to adjust height? (fixed 11mm vs adjustable 11-12mm)

**Needed validation:**
- Build simple test rig: 11mm gap + moving belt + gems
- Run at 5, 15, 25, 35 cm/s
- Observe gem behavior: flat? bouncing? tumbling? jamming?
- Document findings, adjust design if needed

**Impact:** If ceiling doesn't work, alternative needed (lower ceiling? belt cover? side walls only?)

**Risk Level:** **HIGH** - Could require complete redesign of transport approach

### Gap #4: Detection Tunnel Construction (UNDEFINED)

**Problem:** "Black matte cardboard" specified, but no build details.

**Undefined aspects:**
1. How to attach to belt assembly? (adhesive? screws? frame?)
2. How to ensure 15mm slot width accuracy? (laser cut? hand cut?)
3. How to attach mouse sensor to tunnel? (mounting bracket?)
4. How to access for maintenance? (removable? fixed?)
5. Light leakage at joints? (tape? seals? overlaps?)

**Impact:** Without build approach, cannot estimate construction time or complexity.

**Risk Level:** **MEDIUM** - Solvable but needs design work

### Gap #5: Motor Driver & Control (MISSING)

**Problem:** Documentation mentions "Motor PWM" from OpenPLC but no driver specified.

**Missing:**
- Motor driver IC selection (L298N? TB6612? A4950?)
- PWM frequency selection
- Direction control logic
- Speed feedback mechanism (encoder? tachometer?)
- Current monitoring/protection

**Impact:** Cannot implement speed control without driver design.

**Risk Level:** **MEDIUM** - Standard problem but must be designed

---

## Physics Validation Required

### Validation Test #1: Belt Transfer Dynamics

**Objective:** Verify gems transfer cleanly from Belt 1 to Belt 2

**Setup:**
- Build minimum test rig: 2 belts, 20mm gap, adjustable speed
- Use manual gem placement
- High-speed video recording (60+ fps)

**Test Matrix:**

| Belt 1 Speed | Belt 2 Speed | Transfer Geometry | Success Criteria |
|--------------|--------------|-------------------|------------------|
| 5 cm/s | 35 cm/s | Butt joint | Gem crosses without bouncing >5mm |
| 5 cm/s | 35 cm/s | 10mm overlap | Gem crosses smoothly |
| 5 cm/s | 35 cm/s | 10mm gap | Gem clears gap |
| 5 cm/s | 25 cm/s | Butt joint | Reduced acceleration behavior |

**Acceptance:** <5% bounce rate at target speeds

### Validation Test #2: Anti-Bounce Ceiling

**Objective:** Confirm 11mm ceiling prevents tumbling

**Setup:**
- Single belt + acrylic ceiling at 11mm height
- Variable speed: 5, 15, 25, 35 cm/s
- Test with various gem orientations

**Success Criteria:**
- ≤2% gems exit non-flat at 35 cm/s
- No jamming under ceiling
- No static sticking to acrylic

**Failure modes:**
- Gems tumbling → ceiling too high
- Gems jamming → ceiling too low
- Gems sticking → material change needed

### Validation Test #3: Motor Load Characterization

**Objective:** Measure actual torque/power requirements

**Setup:**
- Test motor with representative belt length
- Add weights to simulate gem load
- Measure current at various speeds/loads

**Deliverables:**
- Torque vs speed curve
- Current consumption at operating point
- Required motor specs with 2× safety margin

---

## Action Items (Prioritized)

### P0 - BLOCKING (Must complete before any construction)

1. **[Physics] Calculate motor torque requirements**
   - Belt 1: 50cm length, 5cm/s, rubber belt, ~25g load
   - Belt 2: 100cm length, 35cm/s, rubber belt, ~50g load
   - Include 2× safety margin
   - Specify: stall torque, running torque, RPM, voltage
   - Deliverable: Motor specification sheet
   - Estimated effort: 4 hours

2. **[Physics] Analyze belt transfer dynamics**
   - Model gem behavior at 7× speed jump
   - Identify failure modes (bounce, slip, jam)
   - Recommend transfer geometry (gap/overlap/angle)
   - Deliverable: Transfer design recommendation
   - Estimated effort: 6 hours

### P1 - HIGH (Must validate before finalizing design)

3. **[Build] Construct transfer test rig**
   - 2 belts, adjustable gap/overlap, variable speed
   - Test gem transfer at target speeds
   - Document failure modes, refine design
   - Deliverable: Test report + design recommendations
   - Estimated effort: 8 hours (build + test + analyze)

4. **[Build] Construct ceiling test rig**
   - Single belt + 11mm acrylic ceiling
   - Test at 5-35 cm/s speeds
   - Verify flat gem exit behavior
   - Deliverable: Ceiling validation report
   - Estimated effort: 4 hours (build + test)

### P2 - MEDIUM (Design work for fabrication)

5. **[Design] Design detection tunnel mount**
   - Mounting approach to belt frame
   - Sensor mounting bracket
   - Maintenance access method
   - Light-sealing approach
   - Deliverable: CAD or detailed drawings
   - Estimated effort: 4 hours

6. **[Design] Design motor driver circuit**
   - Select driver IC (L298N vs TB6612)
   - Design PWM control interface
   - Add current monitoring/protection
   - Deliverable: Schematic + BOM
   - Estimated effort: 4 hours

### P3 - LOW (Nice to have)

7. **[Design] Create full mechanical CAD model**
   - Complete 3D model of transport system
   - Assembly drawings
   - BOM with mechanical parts (fasteners, etc.)
   - Deliverable: CAD files + drawings
   - Estimated effort: 16 hours

---

## Dependencies

### My Work Blocks

| Dependent Teammate | Blocked Item | Why |
|-------------------|--------------|-----|
| @servo-engineer | Gate timing calculations | Servo timing depends on belt speed (35cm/s not validated) |
| @procurement-lead | Motor procurement | Motor specs unknown (torque, RPM, voltage) |
| @procurement-lead | Belt material procurement | Belt specifications depend on motor capabilities |
| @servo-engineer | Actuation force requirements | Servo sizing depends on gem stability at transport speed |

### My Work Depends On

| Dependency | From Teammate | Needed For |
|------------|---------------|------------|
| Maximum servo response time | @servo-engineer | Determine minimum safe belt speed |
| Available motor budget | @procurement-lead | Constrain motor selection options |
| Detection zone distance from belt end | @servo-engineer | Verify 50mm ceiling-end assumption |

---

## Questions for Teammates

### For @servo-engineer

1. **Gate timing window:** What's your maximum response time from detection command to servo movement? (This affects my minimum belt speed calculation)

2. **Gate force requirements:** How much force do diverter gates need to deflect gems? (Affects my motor sizing for belt 2 - gems moving at 35cm/s have more momentum)

3. **Gate distance constraints:** Can gates be at 150mm and 300mm from sensor? (Affects my belt 2 length requirement)

### For @procurement-lead

1. **Motor budget:** What's our per-motor budget? (Affects whether I can spec gear motors vs plain DC motors)

2. **Belt material sourcing:** Where can we get appropriate rubber belts? (Length, width, thickness specs needed)

3. **Acrylic availability:** Can we source 3mm clear acrylic locally? (For ceiling)

4. **Motor driver budget:** Separate line item for L298N/TB6612 drivers?

### For @test-engineer

1. **Test rig timeline:** When can we build the transfer test rig? (Need to validate before committing to full build)

2. **Measurement equipment:** Do we have tachometer or high-speed camera for validation tests?

---

## Recommendations

### Immediate (This Week)

1. **DO NOT purchase motors yet** - specs unknown, risk of buying wrong parts
2. **DO NOT build full transport system** - physics unvalidated
3. **DO build transfer test rig** - minimum viable test to validate core assumption
4. **DO build ceiling test rig** - simple single-belt test
5. **CALCULATE motor torque requirements** - blocking procurement

### Short Term (Next 2 Weeks)

1. Complete physics validation (transfer + ceiling tests)
2. Finalize motor specifications
3. Design motor driver circuit
4. Create detection tunnel mounting design

### Long Term (Next Month)

1. Procure all mechanical components per validated specs
2. Build complete transport system
3. Integrate with servo gates and sensor
4. System-level testing

---

## Conclusion

The mechanical transport subsystem is **theoretically sound but practically unvalidated**. The two-belt singulation concept is clever and could work, but we're attempting to skip the critical physics validation step.

**Biggest Risk:** Belt transfer dynamics. If gems don't transfer cleanly from Belt 1 to Belt 2, the entire two-belt concept fails and we need a different approach (single belt with mechanical singulation? vibratory feeder?).

**Recommended Path Forward:**
1. Build simple transfer test rig (2 days)
2. Build simple ceiling test rig (1 day)
3. Validate core assumptions (1 week)
4. Calculate motor specs (1 day)
5. Only THEN proceed to full build

**Estimated Timeline to Ready-for-Build:** 2 weeks (assuming test rigs can be built with available materials)

---

## References

- **Complete System Documentation:** `docs/COMPLETE_SYSTEM_DOCUMENTATION.md` (lines 287-650: Mechanical Design)
- **Handoff Document:** `docs/HANDOFF.md` (lines 151-166: Two-Belt Physics)
- **Related Issues:** [#1 - [Mechanical] Two-Belt Singulation Physics Validation](https://github.com/Parswanadh/candy-sorting-system/issues/1)

---

*Review completed: 2026-03-02*
*Status: READY FOR PEER REVIEW*
*Next action: Cross-review @servo-engineer and @procurement-lead PRs*
