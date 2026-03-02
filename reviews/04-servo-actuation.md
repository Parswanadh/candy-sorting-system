# Servo Actuation Subsystem Review

## Executive Summary

**STATUS:** ⚠️ **CRITICAL RECONSIDERATION REQUIRED**

The 3-stage cascade servo sorting approach (3× SG90 servos = ₹150) is **BUDGET-FRIENDLY** but faces **SEVERE TIMING VALIDATION GAPS** for the target 1,400 gems/min throughput. The original timing analysis assumes 50ms servo actuation time, which is **UNREALISTIC** for SG90 servos in continuous operation.

**Key Findings:**
- ✅ **Budget:** ₹150 for 3× SG90 servos (only 5% of ₹3,000 budget) - EXCELLENT
- ❌ **Timing:** SG90 cannot reliably achieve 50ms cycle times at 1,400 gems/min
- ❌ **Validation:** Zero testing done on servo gate mechanics or timing
- ⚠️ **Architecture:** 3-stage cascade adds complexity without proven benefit

**Recommendation:** For a ₹3,000 Bangalore student project, **ABANDON the 3-stage cascade approach**. Use a **single-servo diverter** or **simplified 2-bin sort** to demonstrate the concept reliably within budget and complexity constraints.

---

## What Was Planned

### Original Design: 3-Stage Cascade Sorting

```
┌─────────────────────────────────────────────────────────────────┐
│                    3-STAGE SERVO CASCADE                         │
│                                                                  │
│  CONVEYOR → ────────────────────────────────────────────────▶   │
│                                                                  │
│              ┌─────────┐    ┌─────────┐    ┌─────────┐          │
│              │ GATE 1  │    │ GATE 2  │    │ GATE 3  │          │
│              │ SG90 #1 │    │ SG90 #2 │    │ SG90 #3 │          │
│              │ 150mm   │    │ 300mm   │    │ 450mm   │          │
│              └────┬────┘    └────┬────┘    └────┬────┘          │
│                   │              │              │                │
│         ┌─────────┴─────────┐    │    ┌─────────┴─────────┐     │
│         │ RED    ORANGE     │    │    │ BLUE PURPLE  BROWN│     │
│         │ BIN-1  BIN-2      │    │    │ BIN-5 BIN-6  BIN-7│     │
│         └───────────────────┘    │    └────────────────────┘     │
│                                  │                               │
│                         ┌─────────┴─────────┐                    │
│                         │ YELLOW GREEN      │                    │
│                         │ BIN-3   BIN-4     │                    │
│                         └───────────────────┘                    │
└─────────────────────────────────────────────────────────────────┘
```

**Planned Components:**
- **Servo Motors:** 3× SG90 (9g micro servos)
- **Actuation:** Diverter gates at 150mm, 300mm, 450mm positions
- **Control:** ESP32 PWM signals via OpenPLC runtime
- **Target:** Sort 7 colors into 7 bins using binary tree logic

**Budget Breakdown (Original Plan):**
| Item | Quantity | Unit Cost | Total |
|------|----------|-----------|-------|
| SG90 Servo Motor | 3 | ₹50 | ₹150 |
| Servo brackets/hardware | 3 | ₹20 | ₹60 |
| **TOTAL** | | | **₹210** |

---

## Current Reality

### What Actually Exists

**Hardware Status:**
| Component | Status | Notes |
|-----------|--------|-------|
| SG90 Servo Motors | ❌ NOT PURCHASED | Not in inventory |
| Servo mounting hardware | ❌ NOT PURCHASED | Not designed yet |
| Gate mechanism design | ❌ NOT DESIGNED | No CAD, no prototype |
| Timing validation | ❌ NOT TESTED | Zero benchmarks |

**Code Status:**
- ❌ No servo control code written
- ❌ No timing validation tests
- ❌ No gate synchronization logic

**Physical Status:**
- ❌ No mechanical prototype
- ❌ No gate geometry designed
- ❌ No belt-servo synchronization tested

---

## Critical Gaps

### 1. TIMING VALIDATION - THE FUNDAMENTAL PROBLEM

**Original Timing Assumption (from documentation):**
```
At 35cm/s belt, 25mm gem spacing:
- Time between gems: 71.4ms
- Servo actuation: 50ms ← THIS IS THE PROBLEM
- Margin: 17ms
```

**Why 50ms is UNREALISTIC for SG90:**

| Parameter | SG90 Spec (Real) | Assumed in Design | Reality Gap |
|-----------|------------------|-------------------|-------------|
| Operating Speed | 0.1 sec/60° (no load) | 50ms round trip | **2× slower** |
| Load Impact | +30-50% with load | Not considered | **Ignored** |
| Settling Time | 10-15ms (oscillation) | Not considered | **Ignored** |
| Return Time | Same as forward | Not considered | **Ignored** |
| **TOTAL CYCLE** | **100-120ms** | **50ms** | **2.4× error** |

**Real Math at 1,400 Gems/Min:**
```
Target: 1,400 gems/min = 23.3 gems/sec = 42.9ms per gem
SG90 Reality: 100-120ms per actuation cycle
PROBLEM: Servo is 2.8× slower than required!
```

**Conclusion:** SG90 CANNOT achieve 1,400 gems/min. The original timing analysis is fundamentally flawed.

### 2. MECHANICAL DESIGN GAP

**Questions That Need Answers:**
- What's the gate geometry? (flap vs diverter vs rotary)
- How to prevent gems from bouncing over the gate?
- What's the gate return mechanism? (spring-loaded vs gravity vs servo return)
- How to handle jams when multiple gems arrive simultaneously?
- What's the clearance tolerance? (gems are 13mm diameter)

**Missing Designs:**
- ❌ Gate CAD model
- ❌ Mounting bracket design
- ❌ Collection bin layout
- ❌ Jam detection mechanism

### 3. SYNCHRONIZATION CHALLENGE

**3-Stage Cascade Complexity:**

For a gem detected at position 0mm:
```
Decision needed at:
- Gate 1 (150mm): t = 150mm / 350mm/s = 428ms from detection
- Gate 2 (300mm): t = 300mm / 350mm/s = 857ms from detection
- Gate 3 (450mm): t = 450mm / 350mm/s = 1286ms from detection

Queue management:
- At 23.3 gems/sec, how many gems in-flight?
- How to track which gem goes to which gate?
- What if two gems need the SAME gate within 120ms?
```

**Example Conflict Scenario:**
```
Gem #1 (Red) detected at t=0ms   → needs Gate 1 at t=428ms
Gem #2 (Orange) detected at t=43ms → needs Gate 1 at t=471ms
Gap: Only 43ms between gate activations
SG90 cycle time: 100-120ms
RESULT: Missed sort, gem #2 goes to wrong bin or gets stuck
```

### 4. POWER CONSUMPTION - NOT ANALYZED

**SG90 Current Draw:**
- Idle: 5-10mA
- Moving (no load): 100-150mA
- Moving (with load): 200-250mA
- Stall (worst case): 400-500mA

**3 Servos Operating:**
- Average draw (1 active, 2 idle): ~200mA
- Peak draw (all moving): ~600mA
- Can ESP32 5V rail supply this? ❓ NOT VALIDATED

### 5. STUDENT PROJECT COMPLEXITY MISMATCH

**Original Plan Complexity:**
- FPGA (₹900) + ESP32 + OpenPLC + 3-stage cascade
- Total system complexity: INDUSTRIAL AUTOMATION LEVEL
- Student constraints: ₹3,000 budget, Bangalore sourcing, limited time

**Reality Check:**
```
This is MORE complex than many senior-year engineering projects!
For a ₹3,000 student project:
❌ FPGA: Too expensive (30% of budget)
❌ 3-stage cascade: Overkill for demonstration
❌ OpenPLC: Unnecessary complexity
```

---

## Action Items (Prioritized)

### 🔴 CRITICAL - Must Resolve Before Any Hardware

1. **ABANDON 3-STAGE CASCADE** (Cost: ₹0, Time: Immediate)
   - Rationale: Too complex, timing impossible with SG90, unnecessary for student project
   - Alternative: Single-servo diverter or simplified 2-bin sort

2. **CHOOSE: FPGA or NO FPGA?** (Cost: ₹0-900, Time: Decision)
   - Option A: **NO FPGA** → Save ₹900, reduce complexity 80%
   - Option B: **WITH FPGA** → Must be highest priority, leaves only ₹2,100 for everything else

3. **Define SORTING SCOPE** (Cost: ₹0, Time: 30 minutes discussion)
   - Option A: **2-bin demo** (e.g., Red vs Not-Red) → Proves concept, achievable
   - Option B: **3-bin sort** (RGB) → Demonstrates color detection + sorting
   - Option C: **7-bin sort** → Original plan, HIGH RISK of failure

### 🟡 HIGH PRIORITY - Before Buying Anything

4. **Research Alternative Actuators** (Cost: Research time, Bangalore prices)
   - **MG90S** (Metal gear, ~₹80): Stronger, more reliable than SG90
   - **28BYJ-48 Stepper** (~₹120): Precise positioning, slower but more reliable
   - **Solenoid** (~₹100): Fast actuation, binary open/close, maybe better for gates
   - **Request @web-researcher for Bangalore prices**

5. **Prototype SINGLE Gate Mechanism** (Cost: ₹50-150, Time: 2 hours)
   - Cardboard prototype with one servo
   - Test: Can SG90 reliably divert a 13mm gem at 35cm/s?
   - Measure: Actual actuation time, settling time, success rate
   - If single gate fails, multi-gate is impossible

6. **SIMPLIFIED Timing Validation** (Cost: ₹0, Time: 1 hour coding)
   - Write Arduino sketch to cycle SG90 at target rates
   - Test: Can SG90 do 100ms cycles reliably? 80ms? 60ms?
   - Document: Maximum reliable cycle time, current consumption, heating

### 🟢 MEDIUM PRIORITY - If Proving Concept

7. **Design Gate Geometry** (Cost: ₹0-200, Time: 3-4 hours)
   - Fusion360 / OnShape CAD model
   - 3D print vs cardboard vs laser-cut acrylic
   - Test: Does gem clear gate without bouncing?

8. **Power Budget Validation** (Cost: ₹0, Time: 1 hour measurement)
   - Measure actual current draw with servo under load
   - Validate: Can ESP32 5V pin supply this? Need external 5V supply?

### 🔵 LOW PRIORITY - Only if Basic System Works

9. **Multi-Gate Synchronization** (Cost: ₹0, Time: 4-6 hours coding)
   - Only after single gate is proven
   - Queue management system
   - Conflict resolution logic

10. **OpenPLC Integration** (Cost: ₹0, Time: 6-8 hours)
    - Only after basic ESP32 servo control works
    - IEC 61131-3 ladder logic implementation
    - May be overkill for student project

---

## Dependencies

### From ESP32 Architect (@esp32-architect)

**BLOCKING QUESTIONS:**
1. **Are we using FPGA or not?** This changes servo timing budget completely
   - With FPGA: 1.5ms detection → 71ms budget for servo (still tight for SG90)
   - Without FPGA: ~35ms detection (TCS3200) → 36ms budget for servo (IMPOSSIBLE for SG90)

2. **What's the actual color sensor?**
   - TCS3200: 20-50ms detection time → Leaves 21-51ms for servo
   - TCS34725: 5-10ms detection time → Leaves 61-66ms for servo
   - This directly affects servo requirements

3. **Can ESP32 handle servo PWM + sensor + comms simultaneously?**
   - ESP32-S has plenty of PWM channels (LEDC controller)
   - But: Real-time constraints vs WiFi/Bluetooth interference
   - Request: Specify exact pin assignments and timing priorities

### From Mechanical Designer (@mechanical-designer)

**BLOCKING QUESTIONS:**
1. **Belt speed: Is 35cm/s achievable with student-grade hardware?**
   - TT motor + DIY belt tracking at 35cm/s → High vibration risk
   - Vibration = gems bouncing = missed sorts
   - What's the MINIMUM viable belt speed for demonstration?

2. **Gem spacing: Can we RELIABLY achieve 25mm spacing?**
   - Two-belt system is complex to build
   - Alternative: Wider spacing = lower throughput = easier servo requirements
   - Trade-off: 50mm spacing = 1/2 throughput = 2× servo budget

3. **Gate mechanism: Can we use gravity instead of servo return?**
   - Spring-loaded gate returns by gravity (faster, no power)
   - Servo only activates to divert (single direction)
   - This could cut servo cycle time by 50%

4. **Jam detection: How do we handle two gems arriving together?**
   - Mechanical solution? (gate geometry)
   - Software solution? (timeout logic)
   - Ignore for demo? (accept some errors)

---

## Questions for Teammates

### For @esp32-architect:

1. **If we drop FPGA, what's the MAXIMUM servo actuation time we can support?**
   - Hint: With TCS3200 (50ms detection) at 71ms gem spacing → Only 21ms for servo
   - This means: NO SG90, need faster actuator (solenoid?)

2. **Can we use ESP32 hardware timers for precise servo timing?**
   - Need microsecond-precision gate triggering
   - Is ESP32-S capable, or do we need FPGA for timing?

3. **What's the ACTUAL code priority?**
   - OpenPLC runtime (complex, "industrial" feel)
   - Or straight Arduino/ESP32 code (simpler, more reliable)?

### For @mechanical-designer:

1. **What's the MINIMUM viable belt speed for a demo?**
   - Can we do 15-20cm/s instead of 35cm/s?
   - Slower = 2× more time for servo = SG90 might work

2. **Can we use a funnel/bin selector instead of diverter gates?**
   - Single movable bin under conveyor
   - Slower, but eliminates multi-gate synchronization
   - Better for student demo?

3. **What if we use AIR instead of servos?**
   - Solenoid air valve (₹100-150)
   - Compressed air from aquarium pump (₹500)
   - Air blast diverts gem (used in industrial sorters)
   - Pros: Very fast, reliable | Cons: Need air source

---

## Budget Reality Check (₹3,000 Total)

### Original Plan - OVERBUDGET

| Item | Cost | % of Budget | Notes |
|------|------|-------------|-------|
| FPGA (Tang Nano 9K) | ₹900 | 30% | Available locally? |
| ESP32-S | ₹350 | 12% | ✓ Have |
| SG90 × 3 | ₹150 | 5% | Cheap, but maybe unusable |
| TCS3200 sensor | ₹120 | 4% | Need to buy |
| TT motors × 2 | ₹100 | 3% | Belt drive |
| Frame materials | ₹500 | 17% | Acrylic, hardware |
| Power supply | ₹300 | 10% | 5V/3A for servos |
| Wires, breadboard, etc. | ₹200 | 7% | Already have some |
| **TOTAL** | **₹2,620** | **87%** | Leaves ₹380 for errors! |

**Risk:** Zero margin for:
- Shipping costs (Bangalore online ordering)
- Failed components (SG90 might not work)
- Missing hardware (mounting brackets, screws)
- Emergency replacements

### Simplified Option A: NO FPGA, 2-BIN Sort

| Item | Cost | % of Budget | Notes |
|------|------|-------------|-------|
| ~~FPGA~~ | ~~₹900~~ | ~~30%~~ | **CUT** |
| ESP32-S | ₹0 | 0% | ✓ Have |
| MG90S × 1 (metal gear) | ₹80 | 3% | Better than SG90 |
| TCS3200 sensor | ₹120 | 4% | Need to buy |
| TT motor × 1 | ₹50 | 2% | Single belt, slower |
| Frame materials | ₹300 | 10% | Cardboard/wood base |
| Power supply | ₹150 | 5% | Smaller, single servo |
| Collection bins (7 plastic cups) | ₹100 | 3% | Local store |
| Wires, hardware | ₹200 | 7% | Basic setup |
| **TOTAL** | **₹1,000** | **33%** | **₹2,000 BUFFER** |

**Pros:**
- ✅ 67% budget remaining for improvements/errors
- ✅ 1/3 the complexity
- ✅ Proven concept (many YouTube examples)
- ✅ Can upgrade later if basic demo works

**Cons:**
- ❌ Only sorts 2 colors initially
- ❌ Slower throughput (demonstration only)
- ❌ Less "impressive" than 7-bin system

### Simplified Option B: With FPGA, Single Gate

| Item | Cost | % of Budget | Notes |
|------|------|-------------|-------|
| FPGA (Tang Nano 9K) | ₹900 | 30% | Keep for course credit |
| ESP32-S | ₹0 | 0% | ✓ Have |
| MG90S × 1 | ₹80 | 3% | Single diverter |
| TCS34725 (better sensor) | ₹200 | 7% | Faster than TCS3200 |
| TT motors × 2 | ₹100 | 3% | Two-belt singulation |
| Frame materials | ₹500 | 17% | Acrylic for demo |
| Power supply | ₹300 | 10% | 5V/3A for FPGA + servo |
| Collection bins | ₹100 | 3% | 7 cups, single gate selects |
| Wires, hardware | ₹200 | 7% | Quality connections |
| **TOTAL** | **₹2,380** | **79%** | **₹620 BUFFER** |

**Pros:**
- ✅ FPGA demonstrates industrial automation (course credit)
- ✅ Single gate eliminates synchronization complexity
- ✅ Better sensor for faster detection
- ✅ Reasonable budget buffer

**Cons:**
- ❌ FPGA availability uncertain in Bangalore
- ❌ Still complex for student project
- ❌ Single gate means sequential sorting (slower)

---

## Recommendations

### For ₹3,000 Student Project in Bangalore

**🎯 RECOMMENDED APPROACH: Option A (Simplified, No FPGA)**

**Phase 1: Proof of Concept (₹500)**
1. Use existing ESP32-S
2. Buy 1× MG90S servo (₹80)
3. Buy TCS3200 sensor (₹120)
4. Build cardboard prototype with single diverter gate
5. Sort RED vs NOT-RED only
6. Target: 60 gems/min (demonstrate, not optimize)

**Phase 2: Scale Up (₹500 more)**
1. If Phase 1 works: Add 2 more servos (₹160)
2. Build simple 3-bin sort (RGB)
3. Target: 200 gems/min

**Phase 3: Optimize (If interested, ₹1,000 more)**
1. Add FPGA if Bangalore source found (₹900)
2. Improve mechanical design (acrylic frame)
3. Target: 1,000+ gems/min

**Key Philosophy:**
> "Fail fast, fail cheap, then scale"

Better to have a WORKING 2-bin sorter for ₹500 than a FAILED 7-bin system for ₹3,000.

---

## Bangalore-Specific Sourcing Notes

**Research Needed (Request @web-researcher):**

1. **SG90/MG90S Servos:**
   - Amazon India: SG90 ~₹50-80, MG90S ~₹80-120
   - Local shops (SP Road): Possibly cheaper, cash-and-carry
   - Query: "SG90 servo motor Bangalore SP Road price 2026"

2. **TCS3200 Color Sensor:**
   - Amazon India: ₹80-150 (search "TCS3200 color sensor module")
   - Robu.in: Possible Bangalore-based seller
   - Query: "TCS3200 color sensor India buy online 2026"

3. **Tang Nano 9K FPGA:**
   - AliExpress: ~₹900 (shipping 2-3 weeks)
   - India distributors? Unknown, need research
   - Alternative: Seeedstudio FPGA (~₹1,200)
   - Query: "Gowin FPGA board India buy 2026"

4. **TT Motors:**
   - Amazon India: ₹50-80 per piece
   - Local electronics shops: ₹30-50
   - Query: "TT gear motor Bangalore price 2026"

5. **Acrylic/Laser Cutting:**
   - Bangalore: Many services (check Justdial)
   - Cost: ~₹500-1000 for custom frame
   - Alternative: Use cardboard/wood for prototype

---

## Testing & Validation Plan

### Test 1: Servo Speed Benchmark (₹0, 1 hour)

**Arduino Code:**
```cpp
#include <Servo.h>
Servo myservo;
int pos = 0;
unsigned long tStart, tEnd;
int cycles = 0;
float totalTime = 0;

void setup() {
  myservo.attach(5);  // GPIO5 on ESP32
  Serial.begin(115200);
}

void loop() {
  tStart = micros();

  // Full actuation cycle
  myservo.write(0);   // Gate open
  delay(10);
  myservo.write(90);  // Gate divert
  delay(10);

  tEnd = micros();
  float cycleTime = (tEnd - tStart) / 1000.0;  // ms

  cycles++;
  totalTime += cycleTime;

  if (cycles % 100 == 0) {
    Serial.print("Cycle "); Serial.print(cycles);
    Serial.print(" avg: "); Serial.print(totalTime / cycles);
    Serial.println(" ms");
  }

  delay(50);  // Simulate gem spacing
}
```

**Success Criteria:**
- ✅ Average cycle time < 60ms → Can attempt 2-bin at 35cm/s
- ⚠️ Average cycle time 60-80ms → Need slower belt (15-20cm/s)
- ❌ Average cycle time > 80ms → SG90 unusable for this project

### Test 2: Gate Prototype (₹50, 2 hours)

**Materials:** Cardboard, tape, 1× MG90S, M&Ms

**Test:**
1. Build simple flap gate
2. Test at different speeds (push gem by hand)
3. Measure success rate (gems correctly diverted)
4. Identify failure modes (bounce over, stuck, etc.)

**Success Criteria:**
- ✅ >90% success rate → Proceed to full build
- ⚠️ 70-90% success rate → Redesign gate geometry
- ❌ <70% success rate → Abandon servo gates, consider alternatives

### Test 3: Power Consumption (₹0, 1 hour)

**Setup:** Multimeter in series with servo power

**Measure:**
- Idle current
- Moving current (no load)
- Moving current (with gem load)
- Peak current (startup/quick reversal)

**Success Criteria:**
- ✅ Average < 200mA → ESP32 can power directly
- ⚠️ Average 200-400mA → Need external 5V supply
- ❌ Average > 400mA → Need dedicated power supply design

---

## Summary

**Original Plan:** 3× SG90, 3-stage cascade, 7-bin sort at 1,400 gems/min
**Reality Check:** SG90 timing is 2.4× slower than assumed, making target IMPOSSIBLE

**Budget Reality:** ₹3,000 total means FPGA + 3× SG90 = ₹1,050 (35% of budget)
**Complexity Reality:** 3-stage cascade + OpenPLC + FPGA = OVERENGINEERED for student project

**Recommended Path Forward:**
1. **STOP** - Don't buy anything yet
2. **TEST** - Prototype single gate with MG90S (₹80)
3. **DECIDE** - Based on test results:
   - If fails: Abandon servo gates, consider alternatives
   - If works: Scale to 2-bin demo, then upgrade

**Critical Questions to Answer:**
1. FPGA or NO FPGA? (₹900 question)
2. 7-bin or 2-bin demo? (Complexity question)
3. SG90 or MG90S or solenoid? (Actuator question)
4. 35cm/s or 15cm/s belt? (Speed question)

**Dependencies:**
- ❌ Cannot finalize servo design until color sensor is chosen
- ❌ Cannot validate timing until belt speed is confirmed
- ❌ Cannot budget until FPGA decision is made

**Next Step:** Request teammates to answer blocking questions before proceeding.

---

**Review by:** @servo-engineer
**Date:** 2026-03-02
**Status:** ⚠️ CRITICAL RECONSIDERATION REQUIRED
**Confidence:** HIGH (based on SG90 specification analysis)
