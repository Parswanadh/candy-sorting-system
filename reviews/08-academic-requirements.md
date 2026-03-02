# Academic Requirements Analysis

## High-Speed Candy Sorting System - NO FPGA Reality Check

**Analysis Date:** March 2, 2026
**Context:** Bangalore-based student project with limited budget
**Critical Constraint:** FPGA NOT available - ESP32-only implementation

---

## EXECUTIVE SUMMARY

**Original Vision vs. Current Reality:**
- FPGA + ESP32 + ADNS-3050 mouse sensor = Industrial-grade automation demonstration
- Reality: ESP32 + TCS3200 color sensor = Student-level sorting project
- Budget Impact: Reduces component cost significantly but changes academic justification
- Academic Impact: Course outcomes CO1-5 still achievable with reframing

---

## COURSE OUTCOME ANALYSIS

### CO1: System Design - ACHIEVABLE (8/10)

Evidence:
- Two-belt singulation system (5cm/s + 35cm/s) - mechanical design
- Detection tunnel with optical isolation - environmental design
- 3-stage cascade sorting (7 bins with 3 servos) - algorithmic efficiency
- Anti-bounce ceiling (11mm height) - geometric constraint design

### CO2: Sensor Integration - REQUIRES REFRAMING (6/10)

Focus on integration challenges:
- Illumination control (RGB LED strobing vs. ambient light)
- Signal conditioning (TCS3200 built-in amplification)
- Timing synchronization (belt position tracking with IR sensor)
- Calibration procedures (baseline subtraction, threshold tuning)

### CO3: Signal Processing - MAJOR GAP (4/10)

Academic Survival Strategies:
- Implement moving average filtering
- Add baseline drift compensation
- Emphasize complete signal chain: physical sensing to digitization to processing to actuation

### CO4: Industrial Automation - FULLY ACHIEVABLE (9/10)

This is the STRONGEST area:
- OpenPLC Runtime on ESP32 (industry-standard platform)
- IEC 61131-3 programming languages (Ladder Logic, Structured Text)
- Separation of control logic from hardware (PLC architecture)
- Safety interlocks and fault handling (industrial practice)

### CO5: Performance Optimization - METRICS CHANGE (7/10)

New Metrics:
- Throughput optimization within timing budget (600-845 gems/min)
- Cost-performance ratio (functional prototype under budget)
- Algorithmic efficiency: Cascade sorting reduces servo count by 57%

---

## BUDGET ANALYSIS

REQUIRED PURCHASES (Bangalore/Online):
- TCS3200 color sensor: Rs. 250-400 (Amazon India / Robu.in)
- SG90 servos x 3: Rs. 150 total
- DC motors x 2: Rs. 100
- Mechanical parts: ~Rs. 500
- OpenPLC: FREE

TOTAL ESTIMATED COST: Rs. 1,000-1,500 (well under budget\!)

---

## FINAL ACADEMIC VIABILITY ASSESSMENT

Can This Project Get Good Grades Without FPGA?

YES, but requires reframing and honest positioning.

Course Outcome Scores:
- CO1: 8/10 (Strong)
- CO2: 6/10 (Acceptable with justification)
- CO3: 4/10 (Major gap - needs work)
- CO4: 9/10 (Excellent - OpenPLC saves us\!)
- CO5: 7/10 (Different metrics, still good)

OVERALL: 6.8/10 - B+ grade achievable with strong execution

Key to Success:
1. Over-deliver on OpenPLC - this is the competitive advantage
2. Add basic signal processing - moving average, baseline compensation
3. Demonstrate working system - 600 gems/min at 70% accuracy
4. Document trade-offs honestly - evaluators appreciate engineering maturity

---

**Analysis Completed:** March 2, 2026
