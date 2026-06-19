# Pinky — AGENTS.md

34-key column-staggered choc split keyboard — a KiCad v10.0 hardware project.  
This repo: PCB, footprints, and symbols only.  
**ZMK firmware is NOT in this repo** — no ZMK config, keymaps, or firmware here.

## Codebase map

| Path | What |
|---|---|
| `PCB/pinky.kicad_sch` | Schematic (rev 0.1) |
| `PCB/pinky.kicad_pcb` | PCB layout, 2-layer, 1.6 mm |
| `PCB/pinky.kicad_pro` | Project settings (DRC rules, net classes, BOM config) |
| `PCB/Pinky.pretty/` | **7 custom footprints** + **4 symbol library files** (`.kicad_sym`). Case-sensitive — dir name is `Pinky.pretty`, not `pinky.pretty` |
| `PCB/fp-lib-table` | Footprint library table — **exactly ONE entry**: `Pinky` → `${KIPRJMOD}/Pinky.pretty` |
| `PCB/sym-lib-table` | Symbol library table — references 4 `.kicad_sym` files inside `Pinky.pretty/` |
| `PCB/README.md` | JLCPCB manufacturing instructions |

## Library gotchas

- **`fp-lib-table` must have exactly one entry.** Two entries (or case-mismatched paths) causes the footprint editor to show nothing.
- **Directory is `Pinky.pretty` (capital P).** KiCad gets confused if paths don't match. If renamed, update both `fp-lib-table` and `sym-lib-table`.
- **`fp-info-cache` is auto-generated** and gets stale (orphaned footprint entries). Delete it to force regeneration.
- **`pinky.kicad_prl`** — auto-generated KiCad UI state (layer visibility, window positions). Not for manual editing.
- **`~*.lck` files** in `PCB/` — KiCad lock files, gitignored by pattern `PCB/~*.lck`.
- **`.bak` files** in `Pinky.pretty/` — remove them, they don't belong in the library directory.

## Custom footprints (7)

All in `Pinky.pretty/` as `.kicad_mod`:

| Footprint | Used for |
|---|---|
| `Diode_SOD123` | 1N4148W SMD diodes (34 per board, bottom side) |
| `JST_PH_reversible` | Battery connector (optional, requires jumper bridge) |
| `Jumper` | Solder jumper pads (JP1-JP4) |
| `Kailh_socket_PG1350_optional_reversible` | Kailh Choc keyswitch sockets (34 per board) |
| `MSK12C02_reversible` | Power switch |
| `nice_nano` | MCU module |
| `ResetSW` | Tactile reset button |

Plus system lib: `MountingHole:MountingHole_2.2mm_M2_ISO7380` (5 per board).

## Custom symbols

5 unique symbol types across 4 `.kicad_sym` files (12 definitions including unit variants):

- `Device_Jumper_NC_Small`, `Device_Jumper_NO_Small` → `Pinky.pretty/device_jumper_*_small.kicad_sym`
- `MJ-4PP-9` → `Pinky.pretty/soflekeyboard-rescue_mj-4pp-9-...kicad_sym`
- `MX_SW_HS`, `nice_nano` → `Pinky.pretty/pinky.kicad_sym`

**Non-obvious mapping:** `Pinky:MX_SW_HS` (symbol) ↔ `Pinky:Kailh_socket_PG1350_optional_reversible` (footprint).

## Hardware & manufacturing

- **MCU:** nice!nano (nRF52840); alternatives with compatible pinout work (e.g. Puchi-BLE)
- **Universal PCB for both halves** — same PCB built twice. Side selected by ZMK firmware at runtime (USB-connected half = central)
- **Solder jumper pads (JP1–JP4):**
  - JST connector mode: bridge JP2+JP3 (top) and JP1+JP4 (bottom)
  - Direct-solder mode: leave open, solder battery wires to PAD1/PAD2
- **Gerber export:** Plot from KiCad to `Pinky_0-2_gerbers.zip` (not tracked — regenerate)
- **JLCPCB key specs:** 120.5 × 85.5 mm, 2 layers, 1.6 mm, black, HASL, no impedance control, **remove order number**

## KiCad conventions

- **Only one footprint library** (`Pinky`). System libs (e.g. `MountingHole`) come from KiCad's global table.
- **DRC:** min track 0.127 mm, min clearance 0.0 mm, min copper-to-edge 0.5 mm. Violations are `error` severity.
- **SMD diodes (1N4148W)** in SOD123 package on **bottom side**.
- **Default net class:** track 0.127 mm, via 0.6/0.3 mm, clearance 0.127 mm.

## What not to modify

- `PCB/.history/`, `PCB/~*.lck`, `PCB/pinky.round-tracks-config` — auto-save and lock files (gitignored)
- `fp-info-cache`, `pinky.kicad_prl` — auto-generated; delete to regenerate
- `*.bak` in `Pinky.pretty/` — remove if they appear

## No CI / no build scripts

Pure KiCad hardware repo. No package manager, no test runner, no CI.  
