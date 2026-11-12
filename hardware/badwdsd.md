# BadWDSD + qCFW

## Overview

Hardware exploit for PS3 Slim/Super Slim using a Raspberry Pi Pico to interact directly with the NOR flash chip. Created by [@Modyfikator89](https://twitter.com/Modyfikator89).

## How It Works

### BadWDSD (Bad Write Data / SD)
```
┌──────────────┐     SPI/Wire     ┌──────────────┐
│   Raspberry  │ ◄──────────────► │   NOR Flash  │
│   Pi Pico    │    (soldered)    │   (PS3 PCB)  │
│   ($4 USD)   │                  │              │
└──────────────┘                  └──────────────┘
        │
        │ USB
        ▼
   PC (dump/write)
```

1. **Physical access**: Solder wires to NOR flash test points on PS3 motherboard
2. **Dump**: Read entire NOR flash via Raspberry Pi Pico
3. **Modify**: Patch boot chain in dump
4. **Write**: Flash modified image back

### qCFW (Quick Custom Firmware)
- Automated tool that takes the NOR dump
- Patches it to load custom firmware
- Writes patched image back to flash
- Result: PS3 boots with CFW regardless of firmware version

## Why It's Unpatchable

- **Below firmware**: Operates at flash chip level, before any code executes
- **Physical access**: Once you have the dump, firmware version is irrelevant
- **No software defense**: The CPU reads whatever is in flash — if flash says "load CFW," it loads CFW
- **Factory reset proof**: Even a full firmware reinstall doesn't touch the patched boot chain

## Hardware Requirements

| Item | Cost | Notes |
|------|------|-------|
| Raspberry Pi Pico | ~$4 | RP2040 microcontroller |
| Solder + wire | ~$5 | Fine gauge, for test points |
| Soldering iron | ~$20+ | Fine tip recommended |
| **Total** | **~$29** | One-time investment |

## Compatibility

| PS3 Model | Flash Type | Compatible |
|-----------|-----------|------------|
| Fat (CECH-0xxx) | NOR/NAND | Yes (but already has CFW via software) |
| Slim (CECH-2xxx) | NOR | **Yes** |
| Slim (CECH-3xxx) | NOR | **Yes** |
| Super Slim (CECH-4xxx) | NOR | **Yes** |

## Complementary to lv0ldr Recovery

| Approach | Method | Skill Level |
|----------|--------|-------------|
| lv0ldr side-channel | Software/analysis | Advanced (research) |
| BadWDSD + qCFW | Hardware/soldering | Intermediate (practical) |

Both achieve the same result (CFW on any PS3), but:
- lv0ldr keys enable **software-only** CFW installation
- BadWDSD enables CFW via **hardware** even without keys
- Together = redundant paths to full access

## Source
- Creator: [@Modyfikator89](https://twitter.com/Modyfikator89)
- Status: Public, tools available
- Documentation: Community-maintained
