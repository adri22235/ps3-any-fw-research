# Sources

## Primary Sources

### Repositories
- [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols) — PS3 NID database (8,141 symbols in ps3_names.txt)
  - `ps3_names.txt` — Function name to NID mappings
  - `ps3.xml` — Structured NID database with hex IDs
  - `nids.txt` — Additional NID listings
  - `db.yml` — YAML-format NID database
  - PR #27 (merged): `ps3_deh_reference.md` — DEH-relevant NIDs organized by category

### People
- **zecoxao** (Jose Coixao) — [GitHub](https://github.com/zecoxao) (81 repos), [Twitter](https://twitter.com/notnotzecoxao)
  - lv0ldr key recovery lead
  - SYSCON firmware key release
  - sce_symbols maintainer
  - Rebug D-REX 4.84 on DECH-3000 demonstration
  
- **@Modyfikator89** — [Twitter](https://twitter.com/Modyfikator89)
  - BadWDSD hardware exploit creator
  - qCFW (Quick Custom Firmware)

### Zecoxao Pinned Repositories
1. `zecoxao.github.io` — Personal site
2. `ps5-pup-decrypt` — PS5 PUP decryption
3. `ps5-pup-unpacker` — PS5 PUP unpacker
4. `sce_symbols` — NID database (where PR #27 was merged)
5. `FTPS5` — FTP server for PS5
6. `ps4norunpack` — PS4 package unpacker

## Related Research

### PS4 (by adri22235)
- [ps4-14-00-security-notes](https://github.com/adri22235/ps4-14-00-security-notes) — 17 commits, WebKit + kernel research
- [ps4-suid-scanner](https://github.com/adri22235/ps4-suid-scanner) — Exploit framework for FW 13.04, 14.00, 14.02

### Academic
- Cell Broadband Engine Architecture documentation (IBM)
- SPE Isolation Mode specification
- Side-channel analysis methodology (general)

## Tools Documentation

### radare2
- [radare2 GitHub](https://github.com/radareorg/radare2)
- Version used: 6.2.6 (Termux aarch64)

### capstone
- [capstone-engine GitHub](https://github.com/capstone-engine/capstone)
- Version used: 5.0.9

### keystone
- [keystone-engine GitHub](https://github.com/keystone-engine/keystone)
- Version used: master (compiled from source)
- Note: PyPI package outdated — must compile from GitHub for CMake 4 compatibility (PR #600)

### Ghidra
- [Ghidra GitHub](https://github.com/NationalSecurityAgency/ghidra)
- Version used: 12.2 PUBLIC

### REA (Reverse Engineer Anything)
- [REA GitHub](https://github.com/morluto/rea)
- Version used: v6.8.0
- Connects to Ghidra, IDA, Frida, Rizin, GDB

## Verification Notes

- All NIDs in this repository verified against source files in zecoxao/sce_symbols
- No hallucinated or assumed data — every NID traceable to ps3_names.txt or ps3.xml
- Hardware exploit claims sourced from developer tweets (noted as single-source where applicable)
- Timeline dates verified with Python datetime calculations
