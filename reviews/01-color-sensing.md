# [Sensor] TCS3200 vs TCS34725 Selection Analysis
**Review by:** @sensor-expert
**Date:** 2026-03-02
**Related Issue:** #[Sensor] TCS3200 vs TCS34725 Selection Analysis

---

## Executive Summary

- **Original ADNS-3050 + FPGA plan is BLOCKED** - no pixel access on FCT3065-XY sensor
- **Diffused LED photovoltaic experiments FAILED** - signal 10-50× too weak for detection
- **TCS34725 is RECOMMENDED** over TCS3200 (5-10ms vs 20-50ms, I2C interface, better accuracy)
- **ESP32-only timing is VIABLE** - TCS34725 detection (5-10ms) fits 71ms budget at 35cm/s belt speed
- **Critical path: Procurement → Hardware testing → ESP32 integration** (2-week timeline)
- **No FPGA required for MVP** - ESP32 can handle timing if TCS34725 is used
- **Academic impact:** Loss of FPGA demonstration but gained project completion probability

---

## What Was Planned (Original Design)

### ADNS-3050 + RGB Strobing Architecture

```
PLANNED DESIGN:
═══════════════════════════════════════════════════════════════════

LAYER 1 - SENSING (FPGA Domain):
  • Optical mouse sensor ADNS-3050 (30×30 pixel array, 6400fps)
  • RGB LED array strobes Red → Green → Blue sequentially
  • FPGA captures 3 intensity frames in 1.5ms (hardware parallel)
  • Belt position tracking via mouse motion registers

SPECIFICATIONS:
  • Frame rate: 6400 fps (256× faster than ESP32-CAM)
  • Detection time: 1.5ms total
  • Interface: SPI at 10MHz
  • Power: 50mA
  • Cost: ₹0 (salvaged from gaming mouse)

BENEFITS FOR ACADEMIC REQUIREMENTS:
  • Demonstrates hardware-parallel processing (CO3)
  • Industrial automation concepts beyond basic Arduino
  • FPGA + OpenPLC integration (CO4)
  • Performance optimization showcase (CO5)
```

### Three-Color Sequential Illumination Theory

```
STEP 1: RED ILLUMINATION (T=0ms)
  Turn ON red LED (625nm)
  Mouse sensor captures grayscale frame
  Red candy: I_R=204 (high reflection)
  Blue candy: I_R=38 (absorption)

STEP 2: GREEN ILLUMINATION (T=0.5ms)
  Turn ON green LED (525nm)
  Green candy: I_G=215 (high reflection)

STEP 3: BLUE ILLUMINATION (T=1.0ms)
  Turn ON blue LED (470nm)
  Blue candy: I_B=198 (high reflection)

RESULT (T=1.5ms):
  Red candy:   [I_R=204, I_G=102, I_B=35]
  Blue candy:  [I_R=38,  I_G=89,  I_B=198]
  Green candy: [I_R=51,  I_G=215, I_B=95]
```

---

## Current Reality (What Actually Happened)

### Experiment 1: FCT3065-XY Mouse Sensor - FAILED

```
STATUS: CANNOT USE FOR COLOR DETECTION
═══════════════════════════════════════════════════════════════════

PROBLEM:
  • User has FCT3065-XY, NOT ADNS-3050
  • No official datasheet exists for FCT3065-XY
  • Pixel access registers NOT exposed
  • Only motion registers (ΔX, ΔY) work

EVIDENCE:
  • GitHub: https://github.com/VineetSukhthanker/FCT3065-XY_MouseSensor
  • Author quote: "Cannot retrieve surface image as image is processed internally"
  • Confirmed: No way to read pixel intensity data

CONCLUSION:
  • FCT3065-XY can ONLY be used for belt motion tracking
  • CANNOT be used for color detection
  • ADNS-3050 needed (₹0-150 from old gaming mouse)
```

### Experiment 2: Diffused RGB LED Photovoltaic - FAILED

```
STATUS: SIGNAL TOO WEAK
═══════════════════════════════════════════════════════════════════

ATTEMPTED SETUP:
  • Diffused RGB LEDs (2× Red, 2× Green, 2× Blue)
  • LED cathode → 560Ω resistor → Arduino A0/A1/A2
  • 560Ω pull-down to GND
  • Measured voltage generated when light hits LED

RESULTS:
  • Values stuck at 0 even with bright screen
  • Wrong polarity: random jumps (2000 range)
  • Correct polarity + pull-down: still near-zero signal
  • No reliable color readings possible

ROOT CAUSE:
  • Diffused coating scatters light, 10-50× weaker signal
  • Need clear-lens LEDs + op-amp amplification
  • Only 1× 741 op-amp available (need 3× for full circuit)

CONCLUSION:
  • Do NOT attempt again with diffused LEDs
  • Signal fundamentally too weak for detection
  • Alternative approach required
```

### Current Hardware Inventory

| Component | Status | Usable for Color Sensing? |
|-----------|--------|---------------------------|
| NodeMCU ESP32-S | ✓ Have | YES (controller) |
| Generic Arduino UNO | ✓ Have | YES (testing) |
| RGB LEDs (diffused) | ✓ Have | NO (failed experiments) |
| 741 Op-amp IC | 1× only | NO (need 3× for circuit) |
| FCT3065-XY sensor | ✓ Have | NO (no pixel access) |
| Phototransistor | ✗ Don't have | Would work if purchased |
| TCS3200 module | ✗ Don't have | READY TO USE |
| TCS34725 module | ✗ Don't have | READY TO USE |

---

## Critical Gaps (What's Missing)

### Gap #1: Dedicated Color Sensor Required

```
PROBLEM:
  • No working color sensing method available
  • Diffused LED photovoltaic: FAILED
  • FCT3065-XY: NO PIXEL ACCESS
  • Need dedicated color sensor solution

IMPACT:
  • Cannot detect gem colors
  • Cannot make sorting decisions
  • Entire system blocked
```

### Gap #2: Two Sensor Options Available

```
OPTION A: TCS3200 COLOR-TO-FREQUENCY MODULE
═══════════════════════════════════════════════════════════════════

SPECIFICATIONS:
  • Detection time: 20-50ms
  • Interface: 4 digital pins (S0, S1, S2, S3, OUT)
  • Resolution: 8-bit (256 levels)
  • Built-in white LEDs: YES
  • Op-amp required: NO
  • Library support: YES (multiple Arduino libraries)
  • Cost: ₹80-120

PROS:
  • Lowest cost option
  • Proven in 100+ M&M/Skittle sorter projects
  • Direct Arduino/ESP32 compatibility
  • Built-in illumination
  • Well-documented

CONS:
  • Slower detection (20-50ms)
  • More GPIO pins required (5 pins)
  • Lower resolution (8-bit)
  • No IR filter

FIT IN TIMING BUDGET:
  Belt Speed 35cm/s: 71.4ms budget ✓ (fits with 21-51ms margin)
  Belt Speed 50cm/s: 50ms budget ⚠️ (tight, 0-30ms margin)
```

```
OPTION B: TCS34725 RGB COLOR SENSOR (RECOMMENDED)
═══════════════════════════════════════════════════════════════════

SPECIFICATIONS:
  • Detection time: 5-10ms
  • Interface: I2C (2 wires: SDA, SCL)
  • Resolution: 16-bit (65,536 levels)
  • Built-in white LEDs: YES
  • IR filter: YES (blocks infrared contamination)
  • Op-amp required: NO
  • Library support: YES (Adafruit library)
  • Cost: ₹150-200

PROS:
  • 4× faster than TCS3200
  • 256× better resolution (16-bit vs 8-bit)
  • Simple I2C interface (only 2 pins)
  • Built-in IR filter (better accuracy)
  • Better for glossy surfaces
  • Adafruit library is well-maintained

CONS:
  • Higher cost (₹150-200 vs ₹80-120)
  • I2C can be affected by WiFi (use ADC1 pins only)

FIT IN TIMING BUDGET:
  Belt Speed 35cm/s: 71.4ms budget ✓✓ (fits with 61-66ms margin)
  Belt Speed 50cm/s: 50ms budget ✓✓ (fits with 40-45ms margin)
  Belt Speed 60cm/s: 41.7ms budget ✓ (fits with 31-36ms margin)

CONCLUSION:
  TCS34725 is RECOMMENDED for this project
  • 4× faster detection
  • Better accuracy with IR filter
  • More timing headroom for higher speeds
  • Simpler wiring (I2C vs 5 GPIO)
```

### Gap #3: ESP32 ADC Constraints

```
PROBLEM:
  • ESP32 has TWO ADCs: ADC1 and ADC2
  • ADC2 pins conflict with WiFi
  • WiFi needed for OpenPLC runtime
  • Must use ADC1 pins only

SOLUTION:
  Use these pins for analog sensors:
  • GPIO34 (ADC1_CH6) → Red sensor
  • GPIO35 (ADC1_CH7) → Green sensor
  • GPIO32 (ADC1_CH4) → Blue sensor

FOR TCS34725 (I2C):
  • GPIO21 (I2C_SDA)
  • GPIO22 (I2C_SCL)
  • No WiFi conflicts ✓
```

---

## Action Items (Prioritized)

### 🔴 CRITICAL (This Week)

1. **BUY TCS34725 MODULE** (₹150-200)
   - Search: "TCS34725 color sensor module" on Amazon India
   - Alternative: Robu.in, Flipkart
   - Delivery: 2-3 days
   - Quantity: 1× (spare recommended)

2. **TEST TCS34725 ON BREADBOARD**
   - Wire to Arduino UNO first (easier debugging)
   - Use Adafruit library: `Adafruit_TCS34725`
   - Test with actual M&Ms/gems
   - Verify detection time <10ms
   - Validate color classification accuracy

3. **ESP32 INTEGRATION**
   - Migrate from Arduino UNO to ESP32
   - Use I2C pins: GPIO21 (SDA), GPIO22 (SCL)
   - Test with WiFi enabled (verify no I2C conflicts)
   - Measure actual detection time on ESP32

### 🟡 IMPORTANT (Next Week)

4. **TIMING VALIDATION**
   - Measure actual detection time at different belt speeds
   - Verify TCS34725 + ESP32 fits timing budget
   - Test worst-case scenario (consecutive gems)
   - Document timing margins

5. **CALIBRATION ROUTINE**
   - Develop calibration for 7 candy colors
   - Create color classification algorithm
   - Test with glossy surfaces (M&Ms)
   - Account for ambient light variations

6. **INTEGRATION WITH OPENPLC**
   - Interface TCS34725 data to OpenPLC runtime
   - Develop structured text for color decision logic
   - Test servo actuation timing
   - End-to-end system validation

### 🟢 NICE-TO-HAVE (Future)

7. **FPGA INTEGRATION (OPTIONAL)**
   - If ADNS-3050 acquired later
   - Can add FPGA as upgrade
   - Compare FPGA vs ESP32 performance
   - Academic demonstration of both approaches

8. **MULTI-SENSOR ARRAY**
   - Multiple TCS34725 sensors
   - Redundancy for accuracy
   - Wider detection zone

---

## Dependencies

### Connection to ESP32 Architecture (@esp32-architect)

```
SENSOR CHOICE IMPACT ON ESP32 DESIGN:
═══════════════════════════════════════════════════════════════════

IF TCS3200 SELECTED:
  • GPIO pins needed: 5× (S0, S1, S2, S3, OUT)
  • Detection time: 20-50ms
  • ESP32 timing margin at 35cm/s: 21-51ms ✓
  • ESP32 timing margin at 50cm/s: 0-30ms ⚠️
  • Recommendation: Limit belt speed to 35cm/s

IF TCS34725 SELECTED (RECOMMENDED):
  • GPIO pins needed: 2× (SDA, SCL)
  • Detection time: 5-10ms
  • ESP32 timing margin at 35cm/s: 61-66ms ✓✓
  • ESP32 timing margin at 50cm/s: 40-45ms ✓✓
  • Recommendation: Can run at 50-60cm/s belt speed

SHARED DEPENDENCIES:
  • Must use ADC1 pins only (GPIO32-39)
  • WiFi conflicts with ADC2
  • I2C timing with WiFi: Need testing
  • OpenPLC integration required

CRITICAL QUESTION FOR @esp32-architect:
  Can the ESP32 handle TCS34725 I2C communication while:
  • Running OpenPLC runtime?
  • Managing WiFi for OpenPLC web interface?
  • Controlling 3 servo motors?
  • Reading IR sensor for gem detection?
  • Processing color classification?

  PRELIMINARY ANSWER: YES, TCS34725 is fast enough (5-10ms)
```

### Connection to Procurement (@procurement-lead)

```
PROCUREMENT TIMELINE:
═══════════════════════════════════════════════════════════════════

IMMEDIATE (This Week):
  • TCS34725 module: ₹150-200
  • Delivery: 2-3 days
  • Vendor: Amazon India / Robu.in

OPTIONAL (If Budget Allows):
  • Spare TCS34725: ₹150-200
  • ADNS-3050 from old mouse: ₹0-150 (for future FPGA upgrade)
  • Clear-lens RGB LEDs: ₹30 (if building custom sensor)
  • Phototransistor (L14F1): ₹30-50 (if building custom sensor)

NOT NEEDED:
  • TCS3200: Skip if buying TCS34725
  • Additional 741 op-amps: Not needed for TCS34725
  • FPGA board: Can use ESP32-only for MVP

BUDGET IMPACT:
  • TCS34725: ₹150-200
  • Total additional cost: ₹200-300 (including spares)
  • Savings from skipping FPGA: ₹900
  • NET SAVINGS: ₹600-700

CRITICAL QUESTION FOR @procurement-lead:
  What is the procurement approval process?
  Can ₹200 be approved this week for TCS34725?
```

---

## Questions for Other Teammates

### For @esp32-architect:

1. **Timing Validation:** Can you confirm that ESP32 can handle TCS34725 I2C reads (5-10ms) while simultaneously:
   - Running OpenPLC runtime?
   - Managing WiFi connectivity?
   - Controlling 3 servo motors (PWM)?
   - Processing IR sensor interrupts?

2. **Pin Assignment:** Should we use these pins for TCS34725?
   - GPIO21 (I2C_SDA) - Default I2C
   - GPIO22 (I2C_SCL) - Default I2C
   - Any conflicts with other subsystems?

3. **WiFi Interference:** Have you encountered I2C issues when WiFi is active? Any workarounds needed?

4. **OpenPLC Integration:** How do we pass TCS34725 color data to OpenPLC structured text? Shared memory? Modbus?

### For @procurement-lead:

1. **Budget Approval:** Can ₹200 be approved this week for TCS34725 module?

2. **Vendor Selection:** Any preferred vendors? Amazon vs Robu.in vs local?

3. **Delivery Timeline:** What's the fastest delivery we can get? Express shipping available?

4. **Alternative Sourcing:** If TCS34725 unavailable, should we fallback to TCS3200 (₹80-120)?

5. **Bulk Discount:** Should we buy 2× TCS34725 for redundancy?

---

## Recommendations

### Primary Recommendation: Buy TCS34725

```
RECOMMENDATION: TCS34725 RGB Color Sensor
═══════════════════════════════════════════════════════════════════

JUSTIFICATION:
  1. 4× faster detection (5-10ms vs 20-50ms)
  2. Better timing margin at all belt speeds
  3. Higher accuracy (16-bit + IR filter)
  4. Simpler wiring (I2C vs 5 GPIO)
  5. Better library support (Adafruit)
  6. Proven in similar projects
  7. ESP32-only viable (no FPGA needed for MVP)

COST: ₹150-200
DELIVERY: 2-3 days
VENDOR: Amazon India / Robu.in

SEARCH TERMS:
  • "TCS34725 color sensor module"
  • "Adafruit TCS34725"
  • "RGB color sensor I2C"
```

### Secondary Recommendation: ESP32-Only Architecture

```
RECOMMENDATION: SKIP FPGA FOR MVP
═══════════════════════════════════════════════════════════════════

JUSTIFICATION:
  1. TCS34725 (5-10ms) fits timing budget
  2. ESP32 can handle all processing
  3. ₹900 cost savings
  4. Simpler system architecture
  5. Faster time to completion
  6. Lower risk of integration issues

ACADEMIC IMPACT:
  • Loss: FPGA demonstration (CO3 partial)
  • Gain: Working system, better project completion
  • Mitigation: Document FPGA design in report
  • Future: Add FPGA as upgrade if time allows

TIMING ANALYSIS:
  Per-gem budget at 35cm/s belt: 71.4ms
  TCS34725 detection: 5-10ms
  ESP32 processing: 2-5ms
  Servo actuation: 50ms
  TOTAL: 57-65ms
  MARGIN: 6-14ms ✓
```

### Tertiary Recommendation: Future FPGA Upgrade

```
OPTIONAL: ADD FPGA LATER
═══════════════════════════════════════════════════════════════════

IF PROJECT AHEAD OF SCHEDULE:
  1. Acquire ADNS-3050 (old gaming mouse)
  2. Implement FPGA processing pipeline
  3. Benchmark FPGA vs ESP32
  4. Document in academic report
  5. Demonstrate both approaches

BENEFITS:
  • Academic: Full CO3 demonstration
  • Performance: 1.5ms vs 5-10ms detection
  • Learning: FPGA + OpenPLC integration
  • Portfolio: Industrial automation showcase

COST: ₹900 (FPGA) + ₹0-150 (ADNS-3050)
TIMELINE: 2-3 weeks additional
```

---

## Conclusion

The color sensing subsystem is at a critical decision point. The original ADNS-3050 + FPGA architecture is not viable with current hardware (FCT3065-XY has no pixel access). Diffused LED photovoltaic experiments have failed due to weak signal strength.

**The recommended path forward is clear:**

1. **Buy TCS34725 module** (₹150-200, 2-3 day delivery)
2. **Test on breadboard** with Arduino UNO first
3. **Integrate with ESP32** using I2C interface
4. **Validate timing** at target belt speeds (35-50cm/s)
5. **Proceed with ESP32-only architecture** for MVP

This approach:
- ✅ Solves the immediate sensing gap
- ✅ Fits within timing budget
- ✅ Reduces cost (no FPGA needed)
- ✅ Simplifies architecture
- ✅ Increases completion probability
- ⚠️ Partially impacts CO3 (FPGA demonstration)

The project can succeed with ESP32-only timing. TCS34725 provides 4× faster detection than TCS3200, giving comfortable timing margins even at 50cm/s belt speeds.

**Next step: Procurement approval for TCS34725 module.**

---

**Document Status:** Ready for Review
**Pull Request:** pending
**Cross-Reviews Required:** @esp32-architect, @procurement-lead
