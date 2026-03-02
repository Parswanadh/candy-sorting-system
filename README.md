# High-Speed Optical Candy Sorting Machine
## Multi-Agent Gap Analysis & Critical Path Review

---

## 📋 Project Overview

**Goal:** Build an automated high-speed color sorting machine for small candy/gems (M&Ms, Gems chocolates)

**Target Performance:**
- Sort 1,400–1,600 gems per minute
- 7 color categories: Red, Orange, Yellow, Green, Blue, Purple, Brown
- Belt speed: 35–50 cm/s

---

## 🚨 Current Status: EARLY HARDWARE TESTING

### Critical Gap Identified

| **Planned Architecture** | **Current Reality** |
|:---|:---|
| FPGA + ADNS-3050 mouse sensor | ❌ FPGA NOT available |
| 1.5ms detection → 1,428 gems/min | ❌ Diffused LED experiments FAILED |
| Full hardware pipeline | ⚠️ Need TCS3200/TCS34725 sensor (₹80-200) |

---

## 📁 Repository Structure

```
candy-sorting-system/
├── docs/
│   ├── COMPLETE_SYSTEM_DOCUMENTATION.md  # Original theoretical design
│   └── HANDOFF.md                        # Current reality & gaps
├── reviews/
│   ├── 01-color-sensing.md               # Sensor selection analysis
│   ├── 02-esp32-architecture.md          # Software-only approach
│   ├── 03-mechanical-transport.md        # Belt physics & singulation
│   ├── 04-servo-actuation.md             # Gate timing & control
│   ├── 05-openplc-integration.md         # IEC 61131-3 compliance
│   ├── 06-procurement-budget.md          # BOM & sourcing
│   ├── 07-testing-validation.md          # Test methodology
│   └── 08-academic-requirements.md       # Course outcomes mapping
└── README.md
```

---

## 👥 Team Analysis

This repository contains gap analysis from 8 specialist teammates:

| Role | Subsystem | Focus Area |
|------|-----------|------------|
| `sensor-expert` | Color Sensing | TCS3200 vs TCS34725 selection |
| `esp32-architect` | ESP32 Software | Architecture without FPGA |
| `mechanical-designer` | Mechanical Transport | Belt physics validation |
| `servo-engineer` | Servo Actuation | 3-stage cascade timing |
| `openplc-specialist` | OpenPLC Integration | IEC 61131-3 setup |
| `procurement-lead` | Procurement & Budget | BOM, cost, sourcing |
| `test-engineer` | Testing & Validation | Methodology & QA |
| `academic-liaison` | Academic Requirements | Course outcomes (CO1-5) |

---

## 🎯 Key Questions Being Addressed

1. **Sensor Selection:** TCS3200 or TCS34725? How to integrate with ESP32?
2. **Architecture:** Can ESP32 handle timing without FPGA? What throughput is realistic?
3. **Mechanical:** Are two-belt physics assumptions valid?
4. **Academic:** Does ESP32-only still meet course requirements without FPGA?

---

## 📊 Deliverables

- [x] Repository initialization
- [ ] 8 GitHub issues (one per subsystem)
- [ ] 8 Pull Requests with detailed reviews
- [ ] Cross-review comments
- [ ] Final consolidated summary report

---

## 📚 Documentation References

- **[Complete System Documentation](docs/COMPLETE_SYSTEM_DOCUMENTATION.md)** - Original FPGA-based design
- **[Handoff Document](docs/HANDOFF.md)** - Current status, failed experiments, next steps

---

## 🔗 Links

- **GitHub Repository:** https://github.com/Parswanadh/candy-sorting-system
- **Project Phase:** Hardware validation / sensor selection
- **Next Milestone:** Reliable color detection on test bench

---

*Multi-Agent Analysis initiated: March 2026*
