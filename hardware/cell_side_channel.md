# Cell Processor Side-Channel Attack (lv0ldr Key Recovery)

## Overview

In September 2026, researchers led by zecoxao successfully recovered lv0ldr signing keys via a side-channel attack on the Cell Broadband Engine's SPE (Synergistic Processing Element).

## Cell Broadband Engine Architecture

```
┌─────────────────────────────────────────────┐
│              Cell Broadband Engine            │
│                                               │
│  ┌─────────┐                                  │
│  │   PPE   │ ← Main processor (GameOS runs here)
│  │ (PPC64) │                                  │
│  └────┬────┘                                  │
│       │ Element Interconnect Bus (EIB)        │
│  ┌────┼────┬────┬────┬────┬────┬────┐        │
│  │SPE0│SPE1│SPE2│SPE3│SPE4│SPE5│SPE6│        │
│  │    │    │    │    │    │    │ ISO │ ← Isolation mode
│  └────┴────┴────┴────┴────┴────┴────┘        │
│                                               │
│  SPE0-5: Available for games/apps             │
│  SPE6:   Security SPE (isolation mode)        │
└─────────────────────────────────────────────┘
```

## Security SPE (Isolation Mode)

### What It Does
- Runs cryptographic operations in hardware isolation
- Code loaded and verified by BootROM — cannot be modified by software
- Handles lv0ldr key operations, SELF decryption, and key derivation
- Memory is NOT accessible from PPE or other SPEs during isolation

### How Isolation Works
1. BootROM loads isolation code into SPE Local Store (256 KB)
2. SPE enters isolation mode — DMA access blocked
3. Crypto operations execute in isolated environment
4. Results passed back via mailbox registers (limited interface)

## Side-Channel Vector

### Type
Timing/power analysis of SPE cryptographic operations.

### Why It Works
- Even in isolation mode, the SPE shares the EIB (bus) with other elements
- Crypto operations create observable timing patterns
- Bus contention reveals information about operations being performed
- No amount of software patching can fix this — it's architectural

### Impact
- lv0ldr signing keys recovered
- These keys sign ALL code in the boot chain
- Cannot be revoked — fused in silicon / boot ROM
- Applies to EVERY PS3 ever manufactured, ANY firmware version

## Historical Context

### PS3 Key Timeline
| Year | Event |
|------|-------|
| 2010 | fail0verflow — ECDSA nonce reuse in metldr |
| 2011 | lv0 keys leaked (from OtherOS/lv2 access) |
| 2011-2026 | lv0ldr remained unbroken (no CFW on Slim/Super Slim) |
| Sep 2026 | lv0ldr keys recovered via Cell side-channel |

### Significance
- 2010-2011 keys only worked on Fat PS3 (CECH-0xxx to CECH-2xxx)
- Slim (CECH-3xxx) and Super Slim (CECH-4xxx) had new lv0ldr
- For 15 years, these newer models could only run HEN (Homebrew Enabler)
- September 2026 breakthrough = **true CFW on ALL PS3 hardware**

## Related Files
- [SYSCON Analysis](syscon.md) — Hardware controller that initiates boot
- [BadWDSD](badwdsd.md) — Hardware flash exploit (complementary approach)
- [Security Syscalls](../nid_analysis/security_syscalls.md) — Syscalls that use these keys

## Source
- Primary: zecoxao and collaborators (September 2026)
- Architecture: Cell Broadband Engine documentation (IBM)
