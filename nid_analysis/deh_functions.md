# DEH Detection Functions

Functions that identify PS3 hardware type — critical for DECH-3000 Debugging Station research.

## cellSsAim* Family

| NID | Function | Returns |
|-----|----------|---------|
| `0x1F80E287` | `cellSsAimIsDEH` | `true` on DECH-3000 Debugging Station |
| `0xC0E39B97` | `cellSsAimIsDEHA` | `true` on DEH-A variant |
| `0xE4A68606` | `cellSsAimIsDEHXB` | `true` on DEH-XB variant |
| `0xE35D54E3` | `cellSsAimIsDEHXB2` | `true` on DEH-XB2 variant |
| `0x8AD55D80` | `cellSsAimIsDEX` | `true` on Development Kit (DECR/DECHA) |
| `0x0F48562D` | `cellSsAimIsCEX` | `true` on Consumer/Retail unit |
| `0x145991B4` | `cellSsAimIsARCADE` | `true` on Arcade unit |
| `0x8F71C2DF` | `cellSsAimGetDeviceType` | Hardware type enum value |
| `0x3B4A1AC4` | `cellSsAimGetDeviceId` | Unique device identifier |
| `0x9AD2E524` | `cellSsAimGetOpenPSID` | OpenPSID (online identity) |
| `0x3F062337` | `cellSsAimChkregPsCode` | Region PS code verification |

## Hardware Types

| Type | Model | Description |
|------|-------|-------------|
| CEX | CECH-xxxx | Consumer/Retail — standard PS3 |
| DEX | DECR-xxxx | Development Kit — full SDK access |
| DEH | DECH-3000 | Debugging Station — hardware debugger |
| DEH-A | — | DEH variant A |
| DEH-XB | — | DEH variant XB |
| DEH-XB2 | — | DEH variant XB2 |
| ARCADE | — | Arcade cabinet unit |

## Usage Context

### For Research
- `cellSsAimIsDEH()` → Gate DEH-specific functionality
- `cellSsAimGetDeviceType()` → Complete hardware identification
- These functions exist in firmware across ALL versions
- NIDs are stable — same hash regardless of firmware version

### For Rebug D-REX
- Rebug D-REX enables DEX functionality on CEX hardware
- These detection functions can be patched to return different types
- zecoxao demonstrated D-REX 4.84 on actual DECH-3000 (Nov 11, 2026)

## Source
All NIDs from `ps3.xml` and `ps3_names.txt` in [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols).
Organized in [ps3_deh_reference.md](https://github.com/zecoxao/sce_symbols/blob/main/ps3_deh_reference.md) (PR #27, merged Nov 12, 2026).
