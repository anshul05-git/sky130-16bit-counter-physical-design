# Results and Verification

**Implementation run:** `counter_classic_fallback_baseline_2026_11_08`  
**Flow:** LibreLane Classic  
**Technology:** SKY130A / `sky130_fd_sc_hd`  
**Recorded flow status:** 80/80 stages completed

## 1. Functional verification

| Item | Result |
|---|---:|
| Self-checking Verilator simulation | 12 PASS / 0 FAIL |
| Testbench status | `ALL TESTS PASSED` |
| Covered behaviors | Asynchronous reset, increment, decrement, enable hold, overflow, underflow |
| RTL changed to fix testbench race | No |

The testbench reset deassertion was moved away from the active clock edge to avoid a simulation race. The counter RTL was not changed for this correction.

## 2. Synthesis snapshot

Nine synthesis strategies were explored. `DELAY 4` was selected for the recorded Classic baseline.

| Metric | Synthesis-stage result |
|---|---:|
| Selected strategy | `DELAY 4` |
| Mapped gate count | 106 |
| Synthesis area | 1,323.7696 µm² |

These are synthesis-stage measurements, not final post-route area figures.

## 3. Die, core, and final cell accounting

| Metric | Recorded value |
|---|---:|
| Die dimensions | 62.495 × 73.215 µm |
| Die area | 4,575.57 µm² |
| Core dimensions | 51.06 × 48.96 µm |
| Core area | 2,499.90 µm² |
| Final standard-cell utilization | 70.5205% |
| Final total instances | 406 |

### Final cell grouping

| Cell group | Count | Area |
|---|---:|---:|
| Standard logic cells | 142 | 1,725.40 µm² |
| Tap cells | 30 | 37.536 µm² |
| Metrics `stdcell` aggregate (logic + tap) | 172 | 1,762.94 µm² |
| Fill-class cells (fillers + decaps) | 234 | 736.957 µm² |
| **Total instances** | **406** | — |

The `stdcell` aggregate already includes the 30 tap cells. Do not add the tap count or tap area to the aggregate a second time. The fill-class total consists of 69 filler cells and 165 decap cells. The aggregate standard-cell area plus fill-class area is approximately equal to the core area.

## 4. Placement and routing

| Stage / metric | Recorded result |
|---|---:|
| Final standard-cell utilization | 70.5205% |
| Global-route wirelength | 4,305 µm |
| Global-route vias | 925 |
| Global-route overflow | 0 reported |
| Detailed-route wirelength | 2,288 µm |
| Detailed-route vias | 877 |
| Final detailed-route violations | 0 |

## 5. Post-route STA summary

The final timing summary covered nine RC/PVT combinations (three extraction conditions multiplied by three library PVT scenarios).

| Metric | Worst reported result | Interpretation |
|---|---:|---|
| Setup WNS | +3.709904 ns | Positive under the applied fallback constraints |
| Hold WNS | +0.109108 ns | Positive under the applied fallback constraints |
| Setup TNS | 0 ns | No setup TNS reported |
| Hold TNS | 0 ns | No hold TNS reported |
| Setup violation count | 0 | None reported |
| Hold violation count | 0 | None reported |
| Maximum-capacitance violations | 0 | None reported |
| Maximum-slew violations | 0 | None reported |

### Worst setup path example

| Property | Value |
|---|---|
| Scenario | `max_ss_100C_1v60` |
| Startpoint | Input `ctrl` |
| Endpoint | Flip-flop `_195_` |
| Data arrival | 6.278806 ns |
| Data required | 9.988710 ns |
| Slack | +3.709904 ns |

### Worst hold path example

| Property | Value |
|---|---|
| Scenario | `min_ff_n40C_1v95` |
| Startpoint | `_194_/Q` |
| Endpoint | `_194_/D` |
| Data arrival | 0.534635 ns |
| Data required | 0.425526 ns |
| Slack | +0.109108 ns |

The run's `report_clock_min_period` output included corner-dependent estimates. These report metrics are not treated as a verified guaranteed maximum operating frequency and are not advertised as Fmax.

## 6. Timing constraints and assumptions

The run used LibreLane's generic fallback SDC. The values below were not sourced from an external system specification.

| Constraint | Applied value |
|---|---:|
| Clock period | 10 ns |
| Input delay | 2 ns |
| Output delay | 2 ns |
| Clock uncertainty | 0.25 ns |
| Clock transition | 0.15 ns |
| Output load | 33.442 fF |
| Time derating | 5% |

**Interpretation:** Slack is positive against these generic fallback assumptions. It is not external-interface timing signoff.

## 7. Physical verification

| Check | Recorded outcome |
|---|---|
| DRC | Passed |
| Netgen LVS | Passed; unique circuit match reported |
| Antenna | Passed |
| Later disconnected-pin check | 0 disconnected pins; 0 critical |
| Final GDSII | Generated and opened in KLayout 0.30.7 |

The run also contains a report of zero setup/hold, maximum-slew, and maximum-capacitance violations in the final timing summary.

## 8. Warnings and limitations

| Item | Recorded evidence | Correct interpretation |
|---|---|---|
| Generic fallback SDC | No external interface timing contract was available | Baseline timing only; not external-interface signoff |
| Clock-property labels | Stage 55 reports `Virtual: yes / Propagated: no`, while timing paths show CTS network delay and propagated annotations | Discrepancy unresolved; do not claim it was reconciled |
| IR-drop setup | `VSRC_LOC_FILES` was not supplied | IR-drop estimate is not validated power-integrity signoff |
| Floating nets | A repair/check stage reported two floating nets; a later disconnected-pin check reported zero | Different checks; retain both reports and do not conflate them |
| Detailed-router LEF58 warning | Unsupported enclosure/cut-class information was reported for a cut layer | Flow completed, but preserve the warning in the engineering record |
| Formal equivalence | EQY step was skipped | No formal-equivalence pass is claimed |
| Wirelength checker | Skipped because no wirelength threshold was configured | No long-wirelength threshold result is claimed |
| Power estimate | Tool-estimated; no activity-based VCD/SAIF basis is established in the evidence summary | Do not describe as measured or activity-accurate power |

## 9. Output inventory observed in the local final directory

The archived final run contained the following output categories. They are listed for traceability; this repository documentation pack does not redistribute these implementation artifacts.

| Output type | Observed artifact |
|---|---|
| GDSII | `gds/counter_up_dwn.gds` |
| LEF / DEF | `lef/counter_up_dwn.lef` / `def/counter_up_dwn.def` |
| OpenROAD database | `odb/counter_up_dwn.odb` |
| Gate-level netlist | `nl/counter_up_dwn.nl.v` |
| Timing constraints | `sdc/counter_up_dwn.sdc` |
| Parasitics | MIN, NOM, MAX SPEF |
| Delay annotation | Nine SDF files |
| Metrics | `metrics.csv` and `metrics.json` |

## 10. Overall result

The recorded project completed functional simulation, the full LibreLane Classic flow, final GDS generation, and the logged DRC/LVS/antenna checks. The nine-scenario post-route timing summary reported positive worst setup and hold slack with zero reported setup/hold TNS and violation counts under generic fallback constraints. The result is an educational physical-design baseline, not a production tapeout/signoff claim.
