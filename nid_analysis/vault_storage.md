# Vault / Secure Storage (cellSsVtrm*)

Secure vault functions for encrypted data storage on PS3.

## Functions

| NID | Function | Description |
|-----|----------|-------------|
| `0xA79070D5` | `cellSsVtrmDecrypt` | Decrypt data from vault |
| `0x3231E012` | `cellSsVtrmDecryptMaster` | Decrypt using master key |
| `0x4FD63C1D` | `cellSsVtrmEncrypt` | Encrypt data to vault |

## Architecture

```
Application Data
       │
       ▼
cellSsVtrmEncrypt (0x4FD63C1D)
       │
       ▼
  Encrypted Vault Storage (flash/HDD)
       │
       ▼
cellSsVtrmDecrypt (0xA79070D5)     ← per-console key
cellSsVtrmDecryptMaster (0x3231E012) ← master key
       │
       ▼
  Decrypted Data
```

## What's Stored in Vault

- Console-specific keys
- EID (Encrypted Individual Data)
- IDPS (Individual Data Protection System)
- Per-game encryption seeds
- Trophy encryption keys
- Account tokens

## Master Key vs Regular Key

- `cellSsVtrmDecrypt` — Uses console-specific key derived from EID root
- `cellSsVtrmDecryptMaster` — Uses a master/factory key, shared across consoles
  - Master key access = can decrypt vault data from ANY console
  - Critical for forensic analysis and backup restoration

## Research Notes

### Connection to SYSCON
- Vault key material may be stored/protected by SYSCON
- SYSCON firmware key release enables analysis of this relationship
- Key hierarchy: SYSCON → EID root → per-console vault key

### Connection to lv0ldr
- lv0ldr keys allow interception of vault operations at boot
- Can observe key derivation in real-time on DEH hardware
- Combined with SYSCON analysis = complete key hierarchy

## Source
NIDs from `ps3.xml` in [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols).
