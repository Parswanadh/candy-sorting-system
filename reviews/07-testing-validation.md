# [Testing] Validation Methodology & QA Workflow
## High-Speed Optical Candy Sorting Machine - Testing & Validation Subsystem Analysis

---

## Executive Summary

**Status:** CRITICAL - No formal testing methodology exists
**Budget Reality Check:** Original FPGA-based testing plan IMPOSSIBLE within 3,000 budget
**Critical Gap:** Project has failed breadboard experiments with NO clear path to validation

**Key Finding:** This is a student project that needs a COMPLETELY different testing approach than the original industrial automation design. The original plan assumed FPGA + expensive sensors + precision mechanics. With 3,000 budget, we need a simplified "good enough" testing methodology.

---

## What Was Originally Planned

### End-to-End System Testing Architecture (The Dream)

```
ORIGINAL TEST PLAN (15,000+ budget):
├── SENSOR VALIDATION
│   ├── ADNS-3050 mouse sensor pixel array testing
│   ├── RGB LED strobe timing verification (0.5ms per color)
│   └── FPGA pipeline latency measurement (target: 1.5ms)
│
├── MECHANICAL VALIDATION
│   ├── Two-belt singulation physics verification
│   ├── Anti-bounce ceiling effectiveness testing
│   └── Belt speed calibration (35-50 cm/s)
│
├── ACTUATION TESTING
│   ├── Servo gate timing validation (3-stage cascade)
│   ├── Throughput measurement (target: 1,400-1,600 gems/min)
│   └── Sorting accuracy per color bin
│
└── SYSTEM INTEGRATION
    ├── OpenPLC program verification
    ├── IEC 61131-3 compliance testing
    └── Full throughput stress testing
```

### Original Test Acceptance Criteria

| Metric | Target | Test Method |
|--------|--------|-------------|
| Detection time | 1.5ms | FPGA logic analyzer |
| Color accuracy | 95% | Manual verification of 1000 gems |
| Throughput | 1,400 gems/min | Counter + stopwatch |
| Spacing reliability | 95% gems at 25mm+ | High-speed camera |
| Sort accuracy | 90% correct bin | Manual bin counting |

**Equipment Needed:** Logic analyzer, oscilloscope, high-speed camera = 50,000+

**Reality:** COMPLETELY UNREALISTIC for student project

---

## Current Reality

### What's Actually Happening (March 2026)

```
CURRENT TEST REALITY:
├── Arduino code compiles and uploads
├── Serial communication works (115200 baud)
├── Basic calibration loop runs
├── DIFFUSED LED PHOTOVOLTAIC EXPERIMENTS FAILED
├── No reliable color detection achieved
├── No FPGA available
├── No proper sensors purchased yet
└── NO FORMAL TEST METHODOLOGY
```

### Failed Experiment Details

**Experiment:** Using diffused RGB LEDs as photovoltaic color sensors
**Result:** Values stuck at 0, signal too weak
**Root Cause:** Diffused lens scatters light, no op-amp amplification
**Lesson Learned:** Need proper color sensor (TCS3200/TCS34725)

**Budget Spent on Failed Tests:** 0 (used existing components)
**Time Spent:** ~10 hours
**Outcome:** Identified need for proper sensor, but NO test plan for next steps

### Critical Gaps in Current Testing

1. **No Sensor Validation Plan** - How to verify TCS3200 works before buying?
2. **No Throughput Measurement Method** - How to count 1,400 gems/min manually?
3. **No Accuracy Testing Framework** - What's "good enough" for student project?
4. **No Integration Test Plan** - How to validate entire system works together?
5. **No Budget for Test Equipment** - Can't afford oscilloscope, logic analyzer

---

## Revised Testing Plan (3,000 Budget Reality)

### Student-Project Testing Philosophy

**PRINCIPLE:** "Good enough for course requirements, not industrial perfection"

**What CAN Be Tested with 0 Equipment:**
- Visual inspection of sorting (manual count)
- Stopwatch timing measurements
- Serial monitor data logging
- Arduino-based counting (trigger on each detection)
- Photo/video documentation for grading

**What CANNOT Be Tested (without expensive equipment):**
- Microsecond-precise timing
- RGB spectral analysis
- Latency profiling
- Signal integrity

### Revised Test Acceptance Criteria (Student Level)

| Metric | Original Target | Student Target | Test Method |
|--------|----------------|----------------|-------------|
| Detection time | 1.5ms | 100ms | Stopwatch + Serial |
| Color accuracy | 95% | 80% | Manual count (100 gems) |
| Throughput | 1,400 gems/min | 300 gems/min | Manual count + timer |
| Sort accuracy | 90% | 75% | Bin counting (100 gems) |
| **Budget** | 15,000+ | **3,000** | Component cost tracking |

**Rationale:** 80% accuracy at 300 gems/min is impressive for a 3,000 student project!

---

## Critical Testing Gaps & Solutions

### Gap 1: How to Validate Sensor Choice Before Buying?

**Problem:** TCS3200/TCS34725 cost 150-600. Can't afford to buy wrong sensor.

**Solutions (Priority Order):**

1. **Library Documentation Review** (0, 30 min)
   - Search GitHub for TCS3200/TCS34725 Arduino projects
   - Check code examples for complexity level
   - Look for candy/skittles sorter projects specifically
   - **Success Criteria:** Find 3+ working M&M/skittles sorter projects

2. **YouTube Verification** (0, 1 hour)
   - Watch M&M sorter build videos
   - Note which sensors they use
   - Check comment sections for problems
   - **Success Criteria:** See real working example with similar budget

3. **Bangalore Store Visit** (0 travel, 2 hours)
   - Visit SP Road electronics shops
   - Ask for TCS3200/TCS34725 availability
   - Get pricing and recommendations
   - **Success Criteria:** Know exact cost and availability

4. **Buy Both If Budget Allows** (500-800 total)
   - If TCS3200 (150) + TCS34725 (300) fits budget
   - Test both, keep better one
   - Return/sell unused one
   - **Success Criteria:** Empirical comparison data

### Gap 2: What's the Breadboard  Prototype Workflow?

**Problem:** No clear path from current failed experiments to working prototype.

**Proposed Workflow:**

```
PHASE 1: SENSOR VALIDATION (Week 1, 150-600)
├── Buy TCS3200 OR TCS34725
├── Breadboard test with single gem
├── Serial monitor output: R,G,B values
├── Test all 7 colors (Red, Orange, Yellow, Green, Blue, Purple, Brown)
└── ACCEPTANCE: Can distinguish 6/7 colors correctly
```

```
PHASE 2: MECHANICAL PROOF-OF-CONCEPT (Week 2, 500-800)
├── Build simple single-belt system
├── Manual feeding (no hopper)
├── One servo gate (2 bins only)
├── Test 50 gems, count accuracy
└── ACCEPTANCE: 75% sort accuracy, 10 gems/min
```

```
PHASE 3: FULL PROTOTYPE (Week 3-4, remaining budget)
├── Add second belt for singulation
├── Add remaining 2 servo gates
├── Build detection tunnel
├── Test 500+ gems, measure actual throughput
└── ACCEPTANCE: Complete system working, documented demo video
```

### Gap 3: How to Measure Actual Throughput?

**Problem:** Can't afford high-speed camera or automated counter.

**Student-Friendly Solutions:**

1. **Arduino Serial Counter** (0)
   - Increment counter each time color detected
   - Print count every 10 seconds
   - Calculate gems/min = (count / time_seconds)  60
   - **Accuracy:** 5%

2. **Manual Stopwatch Method** (0)
   - Fill hopper with 100 gems
   - Start stopwatch when first gem detected
   - Stop when 100th gem sorted
   - Calculate throughput = 100 / time_minutes
   - **Accuracy:** 10% (human reaction time)

3. **Video Analysis** (0 - use phone)
   - Record 30-second video of sorting
   - Count gems in video frame-by-frame
   - Verify Arduino counter accuracy
   - **Documentation value:** High - great for project report!

### Gap 4: What Acceptance Criteria for Each Subsystem?

**Student-Level Acceptance Matrix:**

| Subsystem | Test | Method | Acceptance |
|-----------|------|--------|------------|
| **Color Sensor** | Distinguish 7 colors | Manual gem test | 6/7 correct |
| **Belt Drive** | Consistent speed | Stopwatch + distance | 20% of target |
| **Singulation** | Gem spacing | Visual inspection | 70% separated |
| **Servo Gates** | Actuation speed | Serial log timing | 200ms response |
| **Full System** | Sort accuracy | Bin count (100 gems) | 75% correct |
| **Throughput** | Gems per minute | Arduino counter | 300 gems/min |

**Note:** These are REALISTIC targets for 3,000 budget. Original 1,400 gems/min requires 15,000+ precision mechanics.

---

## Dependencies on All Subsystems

### Critical Path for Testing

```
TEST DEPENDENCIES:

                    PROCUREMENT



                    COLOR SENSING



              MECHANICAL          SERVO             ESP32
             TRANSPORT          GATE TEST          SOFTWARE



                    INTEGRATION TEST

```

### Questions for Each Teammate

**@sensor-expert:**
- Which is easier for students: TCS3200 or TCS34725?
- What's the simplest Arduino library for color detection?
- Can we share one sensor for both detection and belt tracking?

**@mechanical-designer:**
- How to test belt singulation without expensive materials?
- Can we use cardboard/foam board for prototype tunnel?
- What's the minimum viable mechanical setup for testing?

**@servo-engineer:**
- How to test servo gate timing with Arduino?
- What's realistic servo response time with budget components?
- Can we use one servo for initial testing?

**@esp32-architect:**
- Can we build a simple testing dashboard on ESP32?
- How to log test data to SD card for analysis?
- What's the simplest code structure for testing?

**@openplc-specialist:**
- Is OpenPLC too complex for 3,000 project?
- Can we use plain Arduino/C++ for testing first?
- When (if ever) should we migrate to OpenPLC?

**@procurement-lead:**
- What's the BEST sensor under 300?
- Are there cheaper alternatives to SG90 servos?
- Can we get bulk discounts on components?

**@academic-liaison:**
- What are the MINIMUM course requirements we must meet?
- Can we reduce scope and still pass?
- What documentation is needed for grading?

---

## Bangalore-Specific Testing Resources

### Local Testing Support (0-100)

**SP Road Bangalore** - Electronics hub
- Ask shop owners for sensor recommendations
- Test components before buying (some shops allow this)
- Get tips from other students/hobbyists
- **Cost:** Auto/bus fare (~50-100)

**Online Communities:**
- Bangalore Arduino/ESP32 meetup groups
- Facebook groups: "Indian Electronics Hobbyists"
- Reddit: r/ArduinoIndia, r/AskElectronics
- **Cost:** Free

**College Resources (if available):**
- Oscilloscope access in lab?
- 3D printing for mechanical parts?
- Senior student projects as reference?
- **Cost:** Free (but may not be available)

---

## Action Items (Prioritized)

### IMMEDIATE (This Week)

1. **[2 hours]** Research TCS3200 vs TCS34725 on GitHub/YouTube
   - Find 3+ M&M/skittles sorter projects
   - Note which sensors they recommend
   - Check code complexity

2. **[2 hours]** Visit SP Road or check Amazon India
   - Get exact pricing for TCS3200/TCS34725
   - Check availability and lead time
   - Ask shop owners for recommendations

3. **[1 hour]** Define "Minimum Viable Demo" for grading
   - What's the SIMPLEST system that earns passing grade?
   - Document acceptance criteria with @academic-liaison

### SHORT-TERM (Next 2 Weeks)

4. **[4 hours]** Design Phase 1 test plan (sensor validation only)
   - Single gem manual testing
   - All 7 colors, 10 trials each
   - Document results in spreadsheet

5. **[2 hours]** Create test logging template
   - Arduino code for serial logging
   - Spreadsheet for manual data entry
   - Photo/video documentation checklist

6. **[3 hours]** Build mechanical prototype (cardboard/foam board)
   - Simple single-belt setup
   - No hopper (manual feed)
   - One servo gate (2 bins)

### LONG-TERM (Month 1-2)

7. **[8 hours]** Full system integration testing
   - End-to-end workflow validation
   - Throughput measurement
   - Accuracy verification

8. **[4 hours]** Documentation for grading
   - Test results summary
   - Demo video editing
   - Final report preparation

---

## Budget-Allocation Recommendations

### Testing-Only Budget Breakdown

| Item | Cost | Priority | Notes |
|------|------|----------|-------|
| **TCS3200 sensor** | 150 | HIGH | Cheapest proven option |
| **OR TCS34725 sensor** | 300 | HIGH | Better but pricier |
| **Test gems (M&Ms)** | 100 | Medium | Buy 2-3 packets for testing |
| **Cardboard/foam board** | 50 | Medium | For prototype tunnel |
| **SG90 servo (1)** | 50 | Medium | Test with 1 gate first |
| **Small DC motor** | 80 | Low | Can use existing fan motor? |
| **Jumper wires (extra)** | 30 | Low | Already have some |
| **TOTAL (minimum)** | **460** | | Sensor + test materials |
| **TOTAL (recommended)** | **710** | | Better sensor + more materials |

**Remaining Budget:** 2,290-2,540 for other subsystems

**Key Insight:** Testing can be done with 500-700 if we prioritize!

---

## Recommended Testing Workflow for Student Project

### Week-by-Week Plan

```
WEEK 1: SENSOR VALIDATION (150-300)
├── Day 1: Buy TCS3200, Arduino library setup
├── Day 2-3: Test single gem, all 7 colors
├── Day 4-5: Improve detection algorithm
├── Day 6-7: Document results, decide next steps
└── DELIVERABLE: Working color detection (6/7 colors)
```

```
WEEK 2: MECHANICAL PROOF-OF-CONCEPT (200-400)
├── Day 1-2: Build cardboard belt + 1 servo gate
├── Day 3-4: Integrate sensor with mechanical
├── Day 5-6: Test with 50 gems, measure accuracy
├── Day 7: Video documentation
└── DELIVERABLE: Working 2-bin sorter (75% accuracy)
```

```
WEEK 3: SCALE-UP (300-500 remaining)
├── Day 1-3: Add second belt + 2 more gates
├── Day 4-5: Build detection tunnel
├── Day 6: Full system testing (500+ gems)
├── Day 7: Demo video + final documentation
└── DELIVERABLE: Complete 7-bin sorter working demo
```

```
WEEK 4: OPTIMIZATION & GRADING PREP
├── Day 1-2: Improve accuracy (software tuning)
├── Day 3-4: Increase throughput (speed adjustments)
├── Day 5-6: Final report writing
├── Day 7: Practice presentation + demo
└── DELIVERABLE: Project ready for grading!
```

---

## Key Questions for Team Discussion

### For All Teammates:

1. **Scope Question:** Can we reduce from 7 colors to 5 colors and still meet requirements?
   - Yellow/Green are hard to distinguish
   - Could simplify sorting gates from 3-stage to 2-stage

2. **Throughput Question:** Is 300 gems/min impressive enough for student project?
   - Original 1,400/min is unrealistic with 3,000
   - 300/min = 5 gems/sec is still visibly fast

3. **Documentation Question:** What proof of testing is needed for grading?
   - Video demo sufficient?
   - Need detailed test logs?
   - Statistical analysis required?

4. **Risk Question:** What's our backup plan if TCS3200/TCS34725 doesn't work?
   - Return to photodiode + op-amp?
   - Use phone camera method?
   - Reduce project scope further?

---

## Success Metrics for 3,000 Budget

### Minimum Viable Product (MVP) Definition

**MVP = PASSING GRADE:**
- Detects 5+ colors reliably (80% accuracy)
- Sorts into 4+ bins (75% accuracy)
- Throughput 200 gems/min (visible improvement over manual)
- Working demo with video documentation
- Complete build log + code on GitHub
- Cost 3,000

**STRETCH GOAL = EXCELLENT GRADE:**
- All 7 colors detected (85% accuracy)
- All 7 bins working (80% accuracy)
- Throughput 500 gems/min
- Professional documentation (graphs, tables)
- Error handling + calibration routines
- Cost 2,500

**DREAM = A+ GRADE:**
- 1,000+ gems/min (unrealistic but impressive!)
- 90%+ accuracy
- Professional PCB/enclosure
- IEC 61131-3 OpenPLC compliance
- Cost exactly 2,997 (budget optimization!)

---

## Final Recommendations

### What Should Be CUT from Original Plan

1. **FPGA** (900+) - Use ESP32 only, accept slower speed
2. **ADNS-3050 mouse sensor** - No pixel access anyway
3. **Precision machined parts** - Use cardboard/3D printed
4. **Professional detection tunnel** - Use black cardboard
5. **Anti-bounce ceiling** - Try without first, add if needed
6. **OpenPLC for MVP** - Use plain Arduino/C++, add OpenPLC if time
7. **Industrial testing equipment** - Manual + Arduino logging sufficient

### What Should Be KEPT (Simplified)

1. **Two-belt concept** - But can use toy conveyor belts
2. **3-stage cascade** - But with cheaper servos or manual gates
3. **Color detection** - With TCS3200 (cheapest proven option)
4. **Serial logging** - Essential for debugging
5. **Video documentation** - Critical for grading

### What Should BE ADDED

1. **Rapid prototyping mindset** - Fail fast, learn, iterate
2. **Student-level acceptance criteria** - Be realistic!
3. **Bangalore sourcing relationships** - Talk to shop owners
4. **Community engagement** - Ask for help online
5. **Documentation-first approach** - Everything gets logged

---

## Conclusion

The original testing methodology assumed industrial automation standards with 15,000+ budget. With 3,000, we need a COMPLETELY different approach focused on:

1. **Rapid, cheap validation** - Test early with minimal investment
2. **Student-level acceptance** - 80% accuracy is impressive!
3. **Bangalore-specific sourcing** - Use local knowledge
4. **Simplified subsystems** - Cut complexity wherever possible
5. **Documentation over precision** - Good enough for grading

**The key insight:** This is a LEARNING project, not a commercial product. Test methodology should prioritize education over perfection.

---

**Next Steps:**
1. Get pricing from @procurement-lead
2. Create GitHub Issue for testing subsystem
3. Coordinate with @sensor-expert on sensor choice
4. Begin Phase 1 testing immediately after sensor purchase

**Estimated Testing Timeline:** 3-4 weeks
**Estimated Testing Budget:** 500-700
**Probability of Success:** 80% (with realistic expectations!)

---

*Analysis prepared by: Test Engineer teammate*
*Date: March 2, 2026*
*Project Phase: Revised testing methodology for 3,000 budget*