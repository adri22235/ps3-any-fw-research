# SYSCON (System Controller)

## Overview

The SYSCON is an independent microcontroller on the PS3 motherboard that manages hardware-level operations. Its firmware key was released by zecoxao, enabling full analysis.

## Responsibilities

```
┌──────────────────────────────────┐
│            SYSCON                 │
│   (Independent Microcontroller)  │
│                                  │
│  ┌──────────┐  ┌──────────────┐  │
│  │  Power    │  │    Boot      │  │
│  │  Mgmt     │  │   Sequence   │  │
│  └──────────┘  └──────────────┘  │
│  ┌──────────┐  ┌──────────────┐  │
│  │   Fan    │  │  Temperature │  │
│  │  Control │  │  Monitoring  │  │
│  └──────────┘  └──────────────┘  │
│  ┌──────────┐  ┌──────────────┐  │
│  │  Eject   │  │    LED       │  │
│  │  Button  │  │   Control    │  │
│  └──────────┘  └──────────────┘  │
│  ┌──────────────────────────────┐│
│  │  Communication with Cell BE  ││
│  └──────────────────────────────┘│
└──────────────────────────────────┘
```

## Boot Sequence Role

```
Power Button → SYSCON activates
       │
       ▼
SYSCON initializes voltage regulators
       │
       ▼
SYSCON releases Cell BE from reset
       │
       ▼
Cell BootROM executes → lv0ldr → lv0 → lv1 → lv2
       │
       ▼
SYSCON monitors temperature/power throughout
```

### Key Point
SYSCON starts BEFORE the Cell processor. It controls whether the Cell even powers on. Understanding SYSCON = understanding the very first moment of PS3 boot.

## Firmware Key Release

### What Was Released
- Encryption key for SYSCON firmware
- Enables decryption and analysis of SYSCON code
- Released by zecoxao

### What This Enables
1. **Full firmware analysis** — Read and understand SYSCON code
2. **Boot sequence documentation** — Exact power-on sequence
3. **Thermal management** — Fan curves, thermal limits
4. **Error code analysis** — YLOD and other hardware failure indicators
5. **Communication protocol** — How SYSCON talks to Cell BE

## Connection to Other Research

### SYSCON → Vault (cellSsVtrm*)
- Vault encryption keys may be derived from SYSCON-protected values
- SYSCON may hold or protect the root key for per-console encryption
- Analysis path: SYSCON firmware → key storage → vault key derivation

### SYSCON → lv0ldr
- SYSCON initiates boot → Cell runs BootROM → BootROM loads lv0ldr
- SYSCON may verify boot integrity via heartbeat/watchdog
- Understanding SYSCON = understanding boot-time security checks

### SYSCON → NID Functions
- `cellSsUmReadEprom` / `cellSsUmWriteEprom` — EPROM access via SYSCON
- Update manager functions interact with SYSCON for firmware updates
- SYSCON validates update packages before allowing flash write

## Research Vectors

### Accessible Now
- [x] Decrypt SYSCON firmware (key available)
- [x] Static analysis of firmware code
- [x] Document communication protocol with Cell
- [x] Map error codes to conditions

### Requires Hardware
- [ ] Logic analyzer capture of SYSCON ↔ Cell communication
- [ ] Power glitch attacks on SYSCON boot
- [ ] Voltage manipulation during key operations
- [ ] JTAG/SWD access (if debug interface exists)

## Source
- Key release: zecoxao
- Architecture: PS3 service manual documentation
- Hardware analysis: Community research
