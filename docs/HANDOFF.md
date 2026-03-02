# PROJECT HANDOFF DOCUMENT
## High-Speed Optical Gem/Candy Color Sorting Machine
### Complete Context for Continuing Development

---

## 1. PROJECT OVERVIEW

**Goal:** Build an automated high-speed color sorting machine for small candy/gems (like M&Ms or Gems brand chocolates).

**Target Performance:**
- Sort 1,400–1,600 gems per minute
- 7 color categories: Red, Orange, Yellow, Green, Blue, Purple, Brown
- Each gem: ~13mm diameter, glossy colored shell
- Belt speed: 35–50 cm/s

**Project Stage:** Currently in EARLY HARDWARE TESTING phase.
- Original architecture designed (FPGA + ESP32 + mouse sensor)
- Color sensing experiments done on breadboard
- Currently testing simplified RGB LED photovoltaic sensing
- Need to pivot to better color sensing approach (see Section 6)

---

## 2. ORIGINAL SYSTEM ARCHITECTURE (The Full Vision)

### Three-Layer Design

```
LAYER 1 - SENSING (FPGA Domain):
  - Optical mouse sensor ADNS-3050 (30×30 pixel array, 6400fps)
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

### Full System Flow
```
INPUT HOPPER
    ↓ (random bulk gems)
BELT 1 (5 cm/s, 50cm long) — slow feed, natural separation
    ↓ (transfer point)
BELT 2 (35 cm/s, 100cm long) — fast transport, creates 25mm gaps
    ↓
ANTI-BOUNCE CEILING (acrylic, 11mm height, ends 50mm before sensor)
    ↓
BLACK DETECTION TUNNEL (light-isolated)
    ↓
SENSOR ZONE:
  - RGB LED strobing (R→G→B, 0.5ms each)
  - Mouse sensor reads pixel intensity
  - FPGA processes → Color_ID in 1.5ms total
    ↓
3-STAGE SERVO SORTING:
  - Gate 1 (150mm): Red/Orange → 2 bins
  - Gate 2 (300mm): Yellow/Green → 2 bins
  - Gate 3 (end): Blue/Purple/Brown → 3 bins
    ↓
7 COLLECTION BINS
```

### Speed Math
```
Timing budget per gem at 35cm/s belt, 25mm spacing:
- Time between gems: 25mm / 350mm/s = 71.4ms
- FPGA detection: 1.5ms
- OpenPLC processing: 2ms
- Servo actuation: 50ms
- TOTAL USED: ~54ms
- MARGIN: 17ms ✓

At 50cm/s belt:
- Time between gems: 25mm / 500mm/s = 50ms  
- Still fits! ✓
- Throughput: 60,000/42ms = 1,428 gems/min ✓
```

---

## 3. HARDWARE COMPONENTS

### What Was Originally Planned
| Component | Model | Cost | Purpose |
|-----------|-------|------|---------|
| FPGA | GOWIN GW1N-9 (Tang Nano 9K) | ₹900 | Hardware-parallel processing |
| MCU | ESP32 (NodeMCU ESP32-S) | ✓ Have | OpenPLC control logic |
| Mouse sensor | ADNS-3050 (salvage from gaming mouse) | ₹0–150 | Color detection + belt tracking |
| Servo motors | SG90 × 3 | ₹150 | Sorting gates |
| DC motors | Generic TT motor × 2 | ₹100 | Belt drive |
| RGB LEDs | Common cathode × 2 | ✓ Have | Illumination |
| IR sensor | FC-51 or TCRT5000 | ₹30 | Gem detection trigger |

### What the User ACTUALLY HAS Right Now
| Component | Status | Notes |
|-----------|--------|-------|
| NodeMCU ESP32-S | ✓ Have | Board identified from photo |
| Generic Arduino UNO (clone) | ✓ Have | Currently used for testing |
| DC power supply module | ✓ Have | 7-10V input, 3.3V/5V output, USB port, seen in photo |
| RGB LEDs (common cathode) | ✓ Have | 2× Red, 2× Green, 2× Blue — DIFFUSED type (problem!) |
| 741 Op-amp IC | ✓ Have | Only 1× (need 3 for full circuit) |
| Resistors | ✓ Have | 47Ω ×5, 560Ω ×5, 4.7kΩ ×5 |
| Jumper wires | ✓ Have | |
| Breadboard | ✓ Have | |
| FCT3065-XY mouse sensor | ✓ Have | CANNOT use for color (see Section 5) |
| Laptop | ✓ Have | Used as color test source |

### What Needs to Be Bought
| Item | Cost | Priority | Where to Buy |
|------|------|----------|--------------|
| TCS3200 color sensor module | ₹80–120 | 🔴 HIGH | Amazon/Robu.in |
| OR TCS34725 module | ₹150–200 | 🔴 HIGH | Amazon/Robu.in |
| 2× more 741 op-amps OR LM358 dual op-amp | ₹20–30 | 🟡 Medium | Local electronics |
| Phototransistor (L14F1/SFH320) | ₹30–50 | 🟡 Medium | Local electronics |
| ADNS-3050 (from old gaming mouse) | ₹0–150 | 🟢 Later | Old mice / eBay |

---

## 4. KEY DESIGN DECISIONS & RATIONALE

### Why Mouse Sensor Over Camera
| Feature | ESP32-CAM | ADNS-3050 | Winner |
|---------|-----------|-----------|--------|
| Frame rate | 25 fps | 6400 fps | Mouse (256×) |
| Processing | JPEG decode 40ms | Raw pixels 0.3ms | Mouse |
| Cost | ₹679 | ₹0 (salvage) | Mouse |
| Motion tracking | No | Yes (bonus!) | Mouse |

### Why FPGA Over Pure ESP32
- ESP32 software processing per gem: **24.6ms**
- FPGA hardware processing per gem: **1.5ms**
- FPGA is 16× faster due to parallel hardware execution
- ESP32 bottlenecks at 845 gems/min; FPGA enables 1,428+ gems/min
- For course requirements: FPGA demonstrates industrial automation concepts

### Why 3-Stage Cascade Sorting
- 7 individual servos = ₹623
- 3-stage cascade with 3 servos = ₹150
- Cascade achieves same 7-bin sorting at 75% lower cost

### Two-Belt Singulation Physics
```
Belt 1 speed: 5 cm/s (slow feed)
Belt 2 speed: 35 cm/s (fast transport)
Speed ratio: 7×
Input spacing on Belt 1: ~5cm (natural)
Output spacing on Belt 2: 5cm × 7 = 35cm (amplified)
Design target: 25mm minimum spacing → safe ✓
```

### Anti-Bounce Ceiling
- Material: Transparent acrylic, 3mm thick
- Height: 11mm (gem is 13mm — cannot stand upright)
- Ends: 50mm BEFORE detection zone
- Physics: Geometric constraint prevents bouncing/tumbling at high belt speed

### Detection Tunnel
- Material: Black matte cardboard (5–8% reflectivity vs white 90%)
- Purpose: Eliminates ambient light contamination
- Size: 100mm long, 15mm entry/exit slots

---

## 5. SENSOR RESEARCH — DEAD ENDS DOCUMENTED

### FCT3065-XY Mouse Sensor (User has this)
- **Status: CANNOT use for color detection**
- No official datasheet exists
- Pixel access registers NOT exposed (confirmed by GitHub repo)
- Author quote: "Cannot retrieve surface image as image is processed internally"
- Only useful for: belt motion tracking (ΔX, ΔY registers work fine)
- GitHub: https://github.com/VineetSukhthanker/FCT3065-XY_MouseSensor

### Diffused RGB LEDs as Photovoltaic Sensors (Tried and FAILED)
- **Status: FAILED — signal too weak**
- Diffused coating scatters light, 10–50× weaker than clear-lens LEDs
- Testing showed: values stuck at 0 even with bright screen
- When LED polarity was wrong: values jumped randomly (2000 range)
- When correct polarity + 560Ω pull-down: still near-zero signal
- Root cause: Need clear-lens LEDs + op-amp amplification for this to work
- **Conclusion: Do not attempt again with diffused LEDs**

### OnePlus Nord CE4 Phone Camera (Considered)
- 720p @ 60fps for normal video
- 720p @ 240fps for slow motion (but saves to file, not real-time)
- Android Camera2 API cannot access 240fps as live stream
- Processing at 60fps: ~30.7ms per frame
- Thermal throttling risk during extended use
- **Verdict: Possible but high complexity, phone thermal risk**

---

## 6. CURRENT STATUS & WHAT TO DO NEXT

### Experiments Done (Breadboard Testing)
1. ✅ Compiled and uploaded Arduino code successfully
2. ✅ Verified serial communication at 115200 baud
3. ✅ Got baseline calibration working
4. ✅ Confirmed LED photovoltaic effect exists (values change with light)
5. ❌ Failed to get reliable color readings with diffused LEDs
6. ✅ Identified root cause (diffused lens + no amplification)

### Immediate Next Steps (Priority Order)

#### OPTION A: Tonight (₹0, parts on hand)
Build RGB LED (emitter) + Phototransistor (detector) circuit:
```
Wiring:
  Arduino GPIO12 → 240Ω → Red LED anode → cathode → GND
  Arduino GPIO13 → 470Ω → Green LED anode → cathode → GND  
  Arduino GPIO14 → 470Ω → Blue LED anode → cathode → GND

  Phototransistor (any NPN: BC547 with heatshrink removed):
    Collector → 5V
    Emitter → 10kΩ → GND
    Emitter → Arduino A0 (read this)

Algorithm:
  1. Turn on RED LED, delay 10ms, read A0 → store as red_reading
  2. Turn on GREEN LED, delay 10ms, read A0 → store as green_reading
  3. Turn on BLUE LED, delay 10ms, read A0 → store as blue_reading
  4. Find max → that's the dominant color
  Total time: ~35ms per gem ✓
```
Tutorial: https://www.instructables.com/Ultra-Fast-and-Simple-Colour-Sensor/

#### OPTION B: This Week (₹80–200, buy online)
**Recommended: TCS3200 module** (₹80–120 on Amazon)
- Built-in white LEDs
- No op-amp needed
- 100s of M&M/Skittle sorter projects use this exact module
- Works directly with Arduino UNO
- Detection time: 20–50ms (fits budget)
- Search: "TCS3200 color sensor module Arduino" on Amazon/Robu.in

**Better alternative: TCS34725** (₹150–200)
- I2C interface (only 2 wires: A4=SDA, A5=SCL)
- 16-bit resolution, built-in IR filter
- Detection time: 5–10ms
- Adafruit library available

---

## 7. CODE WRITTEN SO FAR

### Arduino Color Sensor Test Code (Stable Version)
Last working version — prints raw + adjusted values every 500ms:

```cpp
// Stable Version — RGB LED photovoltaic test
// Wiring: LED long leg → GND, short leg → 560Ω → A0/A1/A2
//         560Ω pull-down from A0/A1/A2 to GND

#define RED_SENSE   A0
#define GREEN_SENSE A1
#define BLUE_SENSE  A2
#define SPIKE_THRESHOLD 40
#define SAMPLE_COUNT    20
#define DEAD_TIME_MS    400

int baseline_R = 0, baseline_G = 0, baseline_B = 0;
bool detecting = false;
unsigned long tStart = 0, tEnd = 0, lastPrint = 0;

int readAvg(int pin) {
  long s = 0;
  for (int i = 0; i < SAMPLE_COUNT; i++) {
    s += analogRead(pin);
    delayMicroseconds(300);
  }
  return s / SAMPLE_COUNT;
}

void calibrate() {
  Serial.println(F("Calibrating - 2 seconds..."));
  delay(2000);
  long sR=0, sG=0, sB=0;
  for (int i=0; i<100; i++) {
    sR += analogRead(RED_SENSE);
    sG += analogRead(GREEN_SENSE);
    sB += analogRead(BLUE_SENSE);
    delay(10);
  }
  baseline_R = sR/100; baseline_G = sG/100; baseline_B = sB/100;
  Serial.print(F("Baseline R=")); Serial.print(baseline_R);
  Serial.print(F(" G=")); Serial.print(baseline_G);
  Serial.print(F(" B=")); Serial.println(baseline_B);
}

String classify(int r, int g, int b) {
  int total = r + g + b;
  if (total < SPIKE_THRESHOLD) return "DARK";
  float fr=(float)r/total, fg=(float)g/total, fb=(float)b/total;
  int mx = max(r, max(g, b));
  if (r==mx && fr>0.45) return (fg>0.25)?"ORANGE":"RED";
  if (g==mx && fg>0.45) {
    if (fr>0.25) return "YELLOW";
    if (fb>0.25) return "CYAN";
    return "GREEN";
  }
  if (b==mx && fb>0.45) {
    if (fr>0.25) return "MAGENTA";
    if (fg>0.25) return "CYAN";
    return "BLUE";
  }
  return "WHITE";
}

void setup() {
  Serial.begin(115200);
  delay(500);
  Serial.println(F("=== RGB Sensor ==="));
  calibrate();
}

void loop() {
  int rawR=readAvg(RED_SENSE), rawG=readAvg(GREEN_SENSE), rawB=readAvg(BLUE_SENSE);
  int r=max(0,rawR-baseline_R), g=max(0,rawG-baseline_G), b=max(0,rawB-baseline_B);
  int total=r+g+b;

  if (millis()-lastPrint >= 500) {
    lastPrint = millis();
    Serial.print(rawR); Serial.print(F(" | "));
    Serial.print(rawG); Serial.print(F(" | "));
    Serial.print(rawB); Serial.print(F(" || "));
    Serial.print(r); Serial.print(F(" | "));
    Serial.print(g); Serial.print(F(" | "));
    Serial.print(b); Serial.print(F(" | "));
    Serial.println(classify(r,g,b));
  }

  if (!detecting && total > SPIKE_THRESHOLD) { detecting=true; tStart=micros(); }
  if (detecting) {
    String col = classify(r,g,b);
    if (col != "DARK") {
      tEnd = micros();
      float dt = (tEnd-tStart)/1000.0;
      Serial.print(F(">>> ")); Serial.print(col);
      Serial.print(F("  R=")); Serial.print(r);
      Serial.print(F(" G=")); Serial.print(g);
      Serial.print(F(" B=")); Serial.print(b);
      Serial.print(F("  time=")); Serial.print(dt,2); Serial.println(F("ms"));
      detecting=false; delay(DEAD_TIME_MS);
    }
  }
  delay(50);
}
```

### Upload Troubleshooting (Common Issues Faced)
1. **"stk500_getsync not in sync"** with alternating bytes (0x30, 0x09 etc.)
   - Cause: Running sketch is printing to serial, blocking bootloader
   - Fix: Close Serial Monitor → Unplug Arduino → Start upload → Plug in during compile

2. **"Access is denied" on COM port**
   - Cause: Serial Monitor is open and holding the port
   - Fix: Close Serial Monitor completely, then upload

3. **Baud rate garbled output** (symbols like ▒▒▒)
   - Cause: Serial Monitor baud rate mismatch
   - Fix: Bottom-right dropdown in Serial Monitor → select 115200

4. **All readings show 0 even with light**
   - Cause: LED polarity wrong (anode/cathode swapped)
   - Fix: Long leg → GND, Short leg → resistor → Arduino pin

5. **Floating pin random values when LED disconnected**
   - Cause: No pull-down resistor
   - Fix: Add 560Ω from Arduino ADC pin to GND

---

## 8. TIMING ANALYSIS

### Per-Gem Budget at Different Belt Speeds

| Belt Speed | Time Between Gems (25mm spacing) | Max Processing Budget |
|------------|-----------------------------------|----------------------|
| 35 cm/s | 71.4ms | 71.4ms |
| 45 cm/s | 55.6ms | 55.6ms |
| 50 cm/s | 50.0ms | 50.0ms |
| 60 cm/s | 41.7ms | 41.7ms |

### Processing Time by Method

| Method | Detection Time | Fits 35cm/s? | Fits 50cm/s? |
|--------|---------------|--------------|--------------|
| ADNS-3050 + FPGA | 1.5ms | ✅ | ✅ |
| ADNS-3050 + ESP32 | ~10ms | ✅ | ✅ |
| TCS34725 | 5–10ms | ✅ | ✅ |
| TCS3200 | 20–50ms | ✅ | ⚠️ tight |
| RGB LED + Phototransistor | 35ms | ✅ | ⚠️ tight |
| Phone Camera @ 60fps | 33ms | ✅ | ⚠️ tight |
| Diffused LED photovoltaic | ❌ Failed | — | — |

---

## 9. ACADEMIC CONTEXT

This project is for a college course covering industrial automation.

**Course Outcomes (CO) mapping:**
- CO1: System design (two-belt singulation, tunnel design)
- CO2: Sensor integration (mouse sensor, RGB LEDs)
- CO3: Signal processing (FPGA pipeline, HSV conversion)
- CO4: Industrial automation standards (OpenPLC, IEC 61131-3)
- CO5: Performance optimization (timing analysis, throughput)

**Key differentiator for marks:** Using FPGA (hardware-parallel processing) instead of just ESP32 demonstrates understanding of industrial-grade automation beyond basic Arduino projects.

---

## 10. WIRING REFERENCE

### Arduino UNO Pin Assignments (Current Testing)
```
A0 → Red LED cathode (short leg) via 560Ω (pull-down 560Ω to GND)
A1 → Green LED cathode via 560Ω (pull-down 560Ω to GND)
A2 → Blue LED cathode via 560Ω (pull-down 560Ω to GND)
GND → All LED anodes (long legs)
```

### NodeMCU ESP32-S Pin Assignments (Future)
```
GPIO34 (ADC1_CH6) → Red sensor
GPIO35 (ADC1_CH7) → Green sensor
GPIO32 (ADC1_CH4) → Blue sensor
GPIO12 → Red emitter LED (via 560Ω)
GPIO13 → Green emitter LED (via 560Ω)
GPIO14 → Blue emitter LED (via 560Ω)
NOTE: Use ADC1 pins only (ADC2 conflicts with WiFi)
```

### Power Supply Module (DC-DC on breadboard)
```
Input: 7–10V DC barrel jack
Output options: 3.3V or 5V selectable via jumper
USB port: Can power ESP32 simultaneously
Set to 5V for 741 op-amp supply
```

---

## 11. LESSONS LEARNED

1. **Diffused LEDs cannot work as photovoltaic sensors** — signal is too weak without op-amp AND clear lens
2. **FCT3065-XY has no pixel access** — only ΔX/ΔY motion registers exposed, internal image processing blocks direct pixel reading
3. **Floating ADC pins give random readings** — always add pull-down resistors
4. **Arduino upload fails if Serial Monitor is open** — close it before every upload
5. **LED polarity is critical** — long leg = anode (+), short leg = cathode (-); for photovoltaic mode: anode to GND, cathode to ADC pin
6. **Baseline calibration needs true darkness** — hand-covering is insufficient; need black cloth or dark box
7. **ESP32 ADC2 pins conflict with WiFi** — always use ADC1 pins (GPIO32–39) for analog reading

---

## 12. RECOMMENDED IMMEDIATE ACTION

**BUY TCS3200 module** (₹80–120, Amazon India, arrives in 2 days)

This is the single best next step because:
- Proven in 100+ M&M/Skittle sorter projects
- Works directly with Arduino UNO (no op-amp, no complex wiring)
- Built-in white LEDs (no external illumination needed)
- Full documentation + library available
- Detection time fits the timing budget

Search term: "TCS3200 color sensor module" on Amazon India or Robu.in

Alternative if TCS3200 unavailable: TCS34725 (better specs, I2C, ₹150–200)

---

## 13. USEFUL LINKS & REFERENCES

| Resource | URL |
|----------|-----|
| FCT3065-XY GitHub (only existing code) | https://github.com/VineetSukhthanker/FCT3065-XY_MouseSensor |
| Ultra Fast Color Sensor (phototransistor method) | https://www.instructables.com/Ultra-Fast-and-Simple-Colour-Sensor/ |
| TCS3200 Arduino tutorial | https://randomnerdtutorials.com/arduino-color-sensor-tcs230-tcs3200/ |
| TCS34725 Adafruit tutorial | https://learn.adafruit.com/adafruit-color-sensors |
| M&M color sorter (TCS3200) video | https://www.youtube.com/watch?v=g3i51hdfLaw |
| DIY LED color sensor video | https://www.youtube.com/watch?v=jOi6O2I6P9M |
| ADNS-3050 pixel access library | https://github.com/biomurph/MouseEye |
| PAW3205 datasheet (similar to ADNS) | https://www.epsglobal.com/Media-Library/EPSGlobal/Products/files/pixart/PAW3205DB-TJ3T.pdf |

---

*Handoff document generated: March 2026*
*Project phase: Hardware validation / sensor selection*
*Next milestone: Reliable color detection on test bench*
