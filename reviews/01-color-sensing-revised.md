# [Sensor] Budget-Constrained Color Sensing Analysis - ₹3,000 Total Project
**Review by:** @sensor-expert
**Date:** 2026-03-02 (Revised for Budget Constraints)
**Related Issue:** #3 (Revised)

**🚨 CRITICAL BUDGET CONSTRAINT: ₹3,000 TOTAL for entire project**

---

## Executive Summary (Revised)

- **🚨 BUDGET EMERGENCY:** Original plan (₹10,000+) WAY over ₹3,000 total budget
- **❌ CUT:** FPGA (₹900), TCS34725 (₹200-600), industrial components - ALL TOO EXPENSIVE
- **✅ NEW PLAN:** LDR + RGB LED approach (₹50-150 total for color sensing)
- **📍 BANGALORE SOURCING:** SP Road, Amazon.in, Robu.in - student-friendly options
- **🎓 STUDENT LEVEL:** Keep it BASIC - no industrial automation complexity
- **⏱️ TIMING:** LDR approach 50-100ms per gem (slower but WORKS for demo)
- **🎯 REALISTIC:** 5-7 gems/second throughput (300-420 gems/min) for student project

---

## Budget Reality Check

### Original Plan vs Budget Reality

| Component | Original Plan | Budget Reality | Decision |
|-----------|--------------|----------------|----------|
| FPGA Board | ₹900 | **TOTAL BUDGET: ₹3,000** | ❌ CUT |
| TCS34725 Sensor | ₹200-600 | Too expensive (7-20% of budget) | ❌ CUT |
| TCS3200 Sensor | ₹150-300 | Borderline (5-10% of budget) | ⚠️ MAYBE |
| **LDR + RGB LED** | — | **₹50-150** | ✅ **RECOMMENDED** |
| ESP32 | ₹400 | Have already | ✅ USE |
| Servo Motors (3x) | ₹150 | Have already | ✅ USE |
| DC Motors (2x) | ₹100 | Have already | ✅ USE |
| Mechanical Parts | ₹500-1000 | Need to budget | 🟡 SIMPLIFY |

### Revised Budget Allocation (₹3,000 Total)

```
BUDGET BREAKDOWN:
═══════════════════════════════════════════════════════════════════

ALREADY HAVE (₹0):
  • ESP32 NodeMCU
  • Arduino UNO
  • RGB LEDs (diffused)
  • Resistors (47Ω, 560Ω, 4.7kΩ)
  • Jumper wires
  • Breadboard
  • FCT3065-XY sensor (for motion tracking only)

NEED TO BUY (₹3,000 remaining):
  • Color sensing: ₹50-150 (LDR + clear RGB LED)
  • Mechanical (belts, motors, structure): ₹1,500-2,000
  • Servo gates (if not available): ₹150
  • Power supply: ₹200-300
  • Misc (screws, cardboard, etc.): ₹200-400

CRITICAL DECISION:
  Color sensing MUST be under ₹150 to leave room for mechanical!
```

---

## Bangalore-Specific Sourcing Research

### Option 1: LDR + RGB LED (₹50-150) ✅ RECOMMENDED

**Components Needed:**
```
BOM (Bill of Materials):
═══════════════════════════════════════════════════════════════════

1. LDR (Light Dependent Resistor) - GL5516 or similar
   • Price: ₹10-20 each
   • Quantity: 1-3 pieces
   • Source: SP Road, Amazon.in, local electronics shops
   • Total: ₹10-60

2. RGB LED (Clear lens, 5mm) - CRITICAL: Must be CLEAR not diffused!
   • Price: ₹8-15 each
   • Quantity: 1 piece
   • Source: SP Road, Amazon.in
   • Total: ₹8-15

3. Resistors (if not available):
   • 220Ω × 3 (for LED current limiting): ₹2-5
   • 10kΩ × 1 (for LDR pull-down): ₹1-2
   • Total: ₹3-7

4. Optional: Op-amp (LM358 dual op-amp) - for better signal
   • Price: ₹15-25
   • Quantity: 1 piece
   • Source: SP Road, Amazon.in
   • Total: ₹15-25

GRAND TOTAL: ₹50-150 (well within budget!)
```

**Bangalore Sourcing:**
```
LOCAL SHOPS (SP Road, Seshadripuram):
  • Sri Krishna Electronics
  • Avinash Electronics
  • Vishal Electronics
  • RK Electronics
  • Prime Electronics

ONLINE (2-3 day delivery):
  • Amazon.in - Search "LDR 5mm", "RGB LED clear 5mm"
  • Robu.in - Search "light dependent resistor"
  • Electronicscomp.com - LDR, LEDs

WALK-IN PRICE EXPECTATIONS:
  • LDR: ₹10-20 (negotiable in bulk)
  • RGB LED: ₹8-15 (clear lens)
  • Resistors: ₹0.50-2 each
```

### Option 2: TCS3200 Module (₹150-300) ⚠️ STRETCH

**Pros:**
- Built-in white LEDs
- Better accuracy than LDR
- Library support available

**Cons:**
- 5-10% of total budget
- More complex GPIO wiring
- Overkill for student project

**Verdict:** Only if budget allows after mechanical sourcing

### Option 3: TCS34725 Module (₹200-600) ❌ TOO EXPENSIVE

**Verdict:** CUT from plan - use LDR approach instead

---

## How LDR + RGB LED Color Detection Works

### Principle of Operation

```
SIMPLE COLOR DETECTION WITH LDR + RGB LED:
═══════════════════════════════════════════════════════════════════

STEP 1: RED LIGHT (Arduino GPIO12 → RED LED)
  • Red LED turns ON for 10-20ms
  • Light hits candy → reflects back
  • Red candy: HIGH reflection → LDR resistance LOW → ADC value HIGH
  • Blue candy: LOW reflection (absorbs red) → LDR resistance HIGH → ADC value LOW
  • Read A0 → Store as R_value

STEP 2: GREEN LIGHT (Arduino GPIO13 → GREEN LED)
  • Green LED turns ON for 10-20ms
  • Similar process
  • Read A0 → Store as G_value

STEP 3: BLUE LIGHT (Arduino GPIO14 → BLUE LED)
  • Blue LED turns ON for 10-20ms
  • Similar process
  • Read A0 → Store as B_value

STEP 4: COLOR DECISION
  • Compare R_value, G_value, B_value
  • Highest value = dominant color
  • Example: R=200, G=50, B=30 → RED candy

TOTAL TIME: 30-60ms per gem
```

### Circuit Diagram

```
SIMPLE WIRING (No op-amp version):
═══════════════════════════════════════════════════════════════════

          Arduino ESP32
         ┌─────────────┐
         │             │
      5V │             │ GPIO12 (RED)
         │    220Ω     │    ┌──────┐
         ├────/\/\/────┼────│  RED │
         │             │    │  LED │
         │             │    └──┬───┘
         │             │       │
         │             │       ├──┐
         │             │       │  │  Candy reflects light
         │    LDR      │    ┌──┴──┴──┐
         │  ═══════    │    │        │
         │             │    │  ⬤    │ ← Candy on belt
         │             │    └────▲───┘
         │             │         │
         │             │    10kΩ  │
         │             │    ═════ │
         │             │       │  │
GND ◀────┼─────────────┼──────┴──┴───┘
         │             │
         │        GPIO34 (ADC1_CH6)
         │             │
         │   (Read LDR voltage)
         │             │
         └─────────────┘

NOTE: Same GPIO13 → GREEN LED, GPIO14 → BLUE LED
      All LEDs point to same spot on belt
      Single LDR reads reflected light
```

### Code Structure (Arduino/ESP32)

```cpp
// Simplified LDR Color Detection
#define RED_PIN   12
#define GREEN_PIN 13
#define BLUE_PIN  14
#define LDR_PIN   A0  // GPIO34 on ESP32

void setup() {
  pinMode(RED_PIN, OUTPUT);
  pinMode(GREEN_PIN, OUTPUT);
  pinMode(BLUE_PIN, OUTPUT);
  pinMode(LDR_PIN, INPUT);
  Serial.begin(115200);
}

int readColor(int ledPin) {
  digitalWrite(RED_PIN, LOW);
  digitalWrite(GREEN_PIN, LOW);
  digitalWrite(BLUE_PIN, LOW);

  digitalWrite(ledPin, HIGH);
  delay(15);  // 15ms stabilization

  int value = analogRead(LDR_PIN);
  digitalWrite(ledPin, LOW);

  return value;
}

void loop() {
  int r = readColor(RED_PIN);
  int g = readColor(GREEN_PIN);
  int b = readColor(BLUE_PIN);

  // Simple decision: highest value wins
  if (r > g && r > b) Serial.println("RED");
  else if (g > r && g > b) Serial.println("GREEN");
  else if (b > r && b > g) Serial.println("BLUE");

  delay(100);
}
```

---

## Performance Analysis (Realistic for Student Project)

### Timing Budget with LDR Approach

```
REALISTIC TIMING:
═══════════════════════════════════════════════════════════════════

Per Gem Processing Time:
  • Red LED on + stabilization: 15ms
  • Green LED on + stabilization: 15ms
  • Blue LED on + stabilization: 15ms
  • ADC reading + processing: 5ms
  • TOTAL: 50ms per gem

Belt Speed Options:
  • Slow (10cm/s): 100ms between gems → 50ms margin ✓✓✓
  • Medium (20cm/s): 50ms between gems → 0ms margin ⚠️
  • Fast (30cm/s): 33ms between gems → NEGATIVE margin ❌

RECOMMENDED: 10-15cm/s belt speed for student demo

THROUGHPUT:
  • At 10cm/s: ~10 gems/second = 600 gems/minute
  • At 15cm/s: ~6.6 gems/second = 400 gems/minute
  • Target (original): 1400 gems/minute ❌ Not achievable

REALITY: This is a STUDENT PROJECT DEMO, not industrial machine!
```

### Accuracy Expectations

```
ACCURACY ANALYSIS:
═══════════════════════════════════════════════════════════════════

LDR + RGB LED Limitations:
  • Resolution: 10-bit ADC (0-1023) - adequate
  • Accuracy: ~70-80% with good calibration
  • Issues: Ambient light, candy glossiness, LED intensity
  • Colors: Can distinguish 5-7 basic colors

IMPROVEMENTS:
  • Black cardboard tunnel (block ambient light)
  • Calibration routine (measure known colors)
  • Multiple readings + average
  • Op-amp amplification (if accuracy insufficient)

REALISTIC GOAL: 70% accuracy for 7 colors (R, G, B, Y, O, P, Br)
```

---

## Revised Architecture (Student-Friendly)

### Three-Layer Simplified Design

```
STUDENT PROJECT ARCHITECTURE:
═══════════════════════════════════════════════════════════════════

LAYER 1 - SENSING (Arduino UNO or ESP32):
  • LDR + RGB LED color detection (₹50-150)
  • Simple sequential RGB strobing
  • ADC reading + basic thresholding
  • NO FPGA, NO complex processing

LAYER 2 - CONTROL (ESP32):
  • Basic Arduino code (no OpenPLC needed!)
  • Simple if-else logic for color decisions
  • Servo timing calculations
  • Serial output for debugging

LAYER 3 - ACTUATION:
  • 3× SG90 servos (₹150 total)
  • Simple cardboard/plastic gates
  • Manual belt speed control (potentiometer)

KEY SIMPLIFICATIONS:
  • NO FPGA (save ₹900)
  • NO OpenPLC (use simple Arduino code)
  • NO industrial automation complexity
  • Focus on WORKING DEMO
```

---

## Action Items (Revised for Budget)

### 🔴 CRITICAL (This Week - Under ₹150)

1. **BUY LDR + CLEAR RGB LED** (₹50-150 total)
   - LDR (GL5516): ₹10-20
   - Clear RGB LED (5mm): ₹8-15
   - Resistors if needed: ₹3-7
   - Optional: LM358 op-amp: ₹15-25
   - **Source:** SP Road shops or Amazon.in

2. **TEST ON BREADBOARD** (₹0)
   - Use existing Arduino UNO
   - Test with M&Ms or colored paper
   - Verify basic color detection works
   - Measure accuracy with 7 colors

3. **SIMPLIFY CODE** (₹0)
   - Remove OpenPLC dependency
   - Use simple Arduino/C++
   - Basic thresholding logic
   - Serial debugging

### 🟡 IMPORTANT (Next Week - ₹1,500-2,000)

4. **BUILD MECHANICAL** (₹1,500-2,000)
   - Cardboard belt guides (₹50-100)
   - Simple DC motors (have already)
   - Servo gates (have 3×)
   - Collection bins (cardboard boxes)

5. **INTEGRATE SYSTEM** (₹0)
   - Connect LDR sensor to ESP32
   - Add servo control
   - Basic belt speed control
   - End-to-end testing

### 🟢 NICE-TO-HAVE (If budget allows)

6. **IMPROVE ACCURACY** (₹50-100)
   - LM358 op-amp for signal
   - Black cardboard tunnel
   - Better calibration

7. **FUTURE UPGRADES** (Post-project)
   - TCS3200 module (₹150-300)
   - TCS34725 module (₹200-600)
   - FPGA board (₹900)

---

## Bangalore Shopping Guide

### SP Road (Seshadripuram) - Walk-in Guide

```
SHOPPING LIST FOR SP ROAD:
═══════════════════════════════════════════════════════════════════

ITEMS TO BUY:
  1. LDR (Light Dependent Resistor)
     • Ask: "5mm LDR" or "GL5516"
     • Price: ₹10-20
     • Buy: 2-3 pieces (spare)

  2. RGB LED (CLEAR lens, not diffused!)
     • Ask: "RGB LED clear 5mm" or "common anode RGB"
     • IMPORTANT: Must be CLEAR, not diffused
     • Price: ₹8-15
     • Buy: 2 pieces (spare)

  3. LM358 Dual Op-Amp (optional)
     • Ask: "LM358 IC" or "dual op-amp"
     • Price: ₹15-25
     • Buy: 1 piece

  4. Resistors (if needed)
     • 220Ω, 1kΩ, 10kΩ
     • Price: ₹0.50-2 each
     • Buy: 5-10 of each

RECOMMENDED SHOPS:
  • Sri Krishna Electronics
  • Avinash Electronics
  • Prime Electronics

TIPS:
  • Bargain! Prices are negotiable
  • Ask for "student discount"
  • Buy in small quantities
  • Get phone numbers for future needs
```

### Online Ordering (Backup)

```
AMAZON.IN SEARCH TERMS:
  • "LDR light dependent resistor 5mm"
  • "RGB LED clear 5mm common anode"
  • "LM358 dual op-amp IC"

EXPECTED ONLINE PRICES (slightly higher):
  • LDR: ₹20-40
  • RGB LED: ₹15-25
  • LM358: ₹20-35

ALTERNATIVE SITES:
  • Robu.in
  • Electronicscomp.com
  • Flipkart (limited electronics)
```

---

## Academic Impact (Realistic Assessment)

### What's Lost vs Gained

```
ACADEMIC REALITY CHECK:
═══════════════════════════════════════════════════════════════════

LOST (from original plan):
  • FPGA demonstration (CO3 - partial)
  • OpenPLC industrial automation (CO4 - partial)
  • High-speed performance (CO5 - partial)
  • Industrial-grade design

GAINED (realistic for student):
  • WORKING SYSTEM (better than theoretical!)
  • Complete end-to-end demo
  • Practical budgeting experience
  • Creative problem-solving
  • Hands-on integration

COURSE OUTCOME MAPPING (Realistic):
  • CO1 (System Design): ✓ Two-belt singulation works
  • CO2 (Sensor Integration): ✓ LDR + LED demonstrates principle
  • CO3 (Signal Processing): ✓ Basic thresholding shown
  • CO4 (Industrial Standards): ⚠️ Simplified to basic automation
  • CO5 (Performance): ✓ Realistic performance analysis

ACADEMIC STRATEGY:
  • Document WHY simplifications were made (budget constraint)
  • Show original design vs implemented design
  • Discuss trade-offs honestly
  • Demonstrate WORKING system (better than non-working complex system!)
```

---

## Conclusion: Can This Project Be Built for ₹3,000 in Bangalore?

### Short Answer: **YES, with simplifications**

```
REALISTIC PROJECT SCOPE:
═══════════════════════════════════════════════════════════════════

BUDGET: ₹3,000 TOTAL

WHAT'S POSSIBLE:
  ✓ LDR + RGB LED color detection (₹50-150)
  ✓ Single belt or simplified two-belt (₹500-1000)
  ✓ 3 servo sorting gates (₹150 - have already?)
  ✓ ESP32 control (₹400 - have already)
  ✓ Cardboard/wood structure (₹200-400)
  ✓ Basic power supply (₹200-300)
  ✓ Working demo with 5-7 colors

WHAT'S NOT POSSIBLE:
  ✗ FPGA-based processing (₹900)
  ✗ Industrial-grade accuracy
  ✗ 1400 gems/minute throughput
  ✗ TCS34725 sensor (₹200-600)
  ✗ Complex mechanical system

REALISTIC GOAL:
  "Build a working candy color sorter demo that can
   sort 5-7 colors at 300-400 gems/minute using
   budget-friendly components available in Bangalore"

THIS IS ACHIEVABLE AND IMPRESSIVE FOR A STUDENT PROJECT!
```

### Minimum Viable Product (MVP)

```
MVP SPECIFICATION:
═══════════════════════════════════════════════════════════════════

HARDWARE:
  • Color sensing: LDR + RGB LED (₹50-150)
  • Controller: ESP32 (have already)
  • Actuation: 3 servos (have already)
  • Transport: Single belt or gravity feed (₹300-500)
  • Structure: Cardboard/wood (₹200-400)
  • Power: USB or simple adapter (₹100-200)

SOFTWARE:
  • Arduino/C++ code (simple, no OpenPLC)
  • Basic color thresholding
  • Servo timing logic
  • Serial output for debugging

PERFORMANCE:
  • Throughput: 5-10 gems/second (300-600 gems/min)
  • Accuracy: 70-80% with calibration
  • Colors: 5-7 basic colors (R, G, B, Y, O, P, Br)
  • Demo: Continuous sorting for 2-3 minutes

COST: ₹1,000-1,500 (well under ₹3,000 budget!)
TIME: 2-3 weeks to build working demo
```

---

## Next Steps

### Immediate Actions (This Week)

1. **Visit SP Road or order online**
   - Buy LDR + clear RGB LED (₹50-150)
   - Get resistors if needed
   - Optional: LM358 op-amp

2. **Breadboard test**
   - Build simple LDR circuit
   - Test with colored objects
   - Verify basic color detection

3. **Write simple code**
   - Arduino/C++ (no OpenPLC initially)
   - Basic RGB strobing
   - Threshold-based decision

4. **Revise full project plan**
   - Get other teammates to revise their budgets
   - Plan simplified mechanical system
   - Allocate remaining budget

### Questions for Teammates

**@team-lead:**
- Confirm ₹3,000 is FINAL total budget?
- Can we simplify requirements (no OpenPLC, no FPGA)?
- Is 300-400 gems/minute acceptable for demo?

**@mechanical-designer:**
- Can you build simple belt system for ₹500-1000?
- Cardboard acceptable or need acrylic/wood?
- Single belt or gravity feed possible?

**@procurement-lead:**
- SP Road visit this week?
- Who has budget authority?
- Emergency fund available?

---

**Document Status:** REVISED for Budget Constraints
**Next Step:** Team meeting to finalize simplified scope

**REALITY:** This is a student project, not industrial machine. Working demo > complex non-working system!

---

## Sources

- [ElectroFans - DIY Color Detector Using LDR and LED](https://www.electrofans.com/diy-color-detector-using-ldr-and-led/)
- [RGB Color Sorting Machine with LDR](https://create.arduino.cc/projecthub/ansh29199/rgb-color-sorting-machine-with-ldr)
- [TCS3200 Color Sensor Module Pricing](https://www.alibaba.com/showroom/tcs3200-color-sensor-module.html)
- [Alibaba Sensor Pricing Reference](https://www.alibaba.com/trade/search?SearchText=tcs34725+color+sensor)
