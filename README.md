# ZVS Induction Heater — 12V PCB

A self-resonant **ZVS (Zero Voltage Switching) Royer oscillator** induction heater built around two IRF540N power MOSFETs. Designed and fabricated as a custom two-layer PCB with a dedicated copper pour on the high-current output path and a full ground pour on the bottom layer.

---

## Topology

The circuit is a **push-pull ZVS (Royer) oscillator** — a self-oscillating topology that requires no external gate drive signal or microcontroller. The two MOSFETs alternate switching states driven entirely by the feedback from the LC resonant tank, achieving near-zero voltage across the drain at each switching transition to minimize switching losses.

```
         +12V
          |
         L1 (100µH)        L3 (100µH)
          |                    |
     D1 (UF4007)          D2 (UF4007)
          |                    |
    Q1 Gate ─── R1 (220Ω)     R2 (220Ω) ─── Q2 Gate
          |                    |
     Q1 Drain ─── C1+C2 ─── Q2 Drain
     (IRF540N)   (330nF ea)  (IRF540N)
          |                    |
         GND                  GND
                  |
               OUT (J1)
```

**How it self-oscillates:**
1. At power-on, slight component imbalance causes one MOSFET to conduct first
2. Current ramps through its inductor half, storing energy in the LC tank
3. Tank voltage feedback cross-drives the opposite gate, toggling both MOSFETs
4. The cycle repeats at the LC resonant frequency — no external clock needed
5. UF4007 fast-recovery diodes suppress inductive spikes and protect MOSFET body diodes

**Estimated resonant frequency:** ~19.6 kHz (100µH, 2×330nF in parallel)

---

## Schematic

![Schematic](schematic.png)

---

## Bill of Materials

| Qty | Reference | Component         | Value / Part      | Notes                                   |
|-----|-----------|-------------------|-------------------|-----------------------------------------|
| 2   | Q1, Q2    | N-Channel MOSFET  | IRF540N (TO-220)  | 100V, 33A, low Rds(on)                  |
| 2   | D1, D2    | Fast Recovery Diode | UF4007          | 1A, 1000V, 75ns trr — freewheeling      |
| 2   | R1, R2    | Resistor          | 220Ω, 0.25W       | Gate drive resistors                    |
| 2   | L1, L3    | Inductor          | 100µH             | Work coil halves / tank inductors       |
| 2   | C1, C2    | Capacitor         | 330nF             | Resonant tank capacitors                |
| 1   | J1        | Screw Terminal    | 2-pin             | Output to work coil                     |
| 1   | J2        | Screw Terminal    | 2-pin             | 12V DC input                            |

---

## PCB Design Notes

- **Layer stackup:** Two-layer board (signal/power top, GND bottom)
- **Ground pour:** Full copper pour on bottom layer — reduces ground impedance and improves heat spreading
- **Output copper pour:** Wide copper pour on the high-current output path (J1 side) to handle switching currents without excessive trace heating
- **MOSFET placement:** Q1 and Q2 placed symmetrically to match parasitics and keep tank layout balanced
- **Gate resistors:** Placed close to MOSFET gates to damp any ringing on the gate drive path
- **Decoupling:** Capacitors placed close to drain nodes to minimize stray inductance in the tank loop
- **Thermal:** TO-220 footprints exposed for heatsink attachment — recommended at sustained load

---

## Electrical Characteristics

| Parameter         | Value              |
|-------------------|--------------------|
| Input voltage     | 12V DC             |
| Self-resonant freq| ~19.6 kHz          |
| Output            | High-frequency AC to work coil |
| MOSFET Vds max    | 100V (IRF540N)     |
| MOSFET Id max     | 33A (IRF540N)      |

---

## Safety

- Do **not** operate without a work coil or load connected — unloaded ZVS circuits can develop dangerous voltage spikes across the drain nodes
- MOSFETs will run hot under sustained load — attach heatsinks
- Keep switching circuitry away from metallic objects not intended as the heating target
- Input supply must be current-limited or fused

## License

MIT License. See `LICENSE` for full terms.
