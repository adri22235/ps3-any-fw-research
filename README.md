# PS3 Any-Firmware Security Research

Investigación de seguridad de PS3 aplicable a **cualquier firmware** — desde retail (CEX) hasta Debugging Stations (DECH-3000).

## Scope

Este repositorio documenta vectores de investigación que NO dependen de una versión específica de firmware:

- **NID Resolution** — Funciones de seguridad identificadas en `sce_symbols`
- **Hardware-Level** — Exploits que operan por debajo del firmware (BootROM, SYSCON, Cell SPE)
- **DEH/DEX Detection** — Cómo el sistema identifica hardware de desarrollo
- **Crypto Primitives** — Funciones criptográficas del sistema (`cellCryptoPu*`)
- **Security Syscalls** — Interfaz kernel para autenticación, DRM y cifrado

## Key Findings

### lv0ldr Key Recovery (September 2026)
- Side-channel en Cell processor
- Liderado por zecoxao y colaboradores
- Abre CFW en **PS3 Slim/Super Slim** (todos los firmwares)
- Impacto: Hardware que antes era imposible de modificar

### BadWDSD + qCFW
- Exploit hardware vía **Raspberry Pi Pico**
- Targets: PS3 Slim/Super Slim NOR
- **Unpatchable** — opera a nivel de flash, por debajo del firmware
- Por [@Modyfikator89](https://twitter.com/Modyfikator89)

### SYSCON Firmware Key
- Clave de firmware SYSCON liberada por zecoxao
- Permite análisis completo del controlador de sistema
- Aplica a todas las revisiones de hardware

### DEH Detection Functions
- `cellSsAimIsDEH` (NID: `0x1F80E287`) — Detecta DECH-3000
- `cellSsAimIsDEHA`, `cellSsAimIsDEHXB`, `cellSsAimIsDEHXB2` — Variantes
- `cellSsAimGetDeviceType` — Enum completo de tipos de hardware
- Referencia completa: [ps3_deh_reference.md en sce_symbols](https://github.com/zecoxao/sce_symbols/blob/main/ps3_deh_reference.md) (PR #27, merged)

## Structure

```
├── README.md                    # Este archivo
├── RESEARCH.md                  # Investigación detallada
├── SOURCES.md                   # Fuentes y referencias
├── nid_analysis/
│   ├── deh_functions.md         # Funciones DEH/DEX detection
│   ├── security_syscalls.md     # Syscalls de seguridad documentados
│   ├── crypto_primitives.md     # cellCryptoPu* (51 funciones)
│   ├── vault_storage.md         # cellSsVtrm* (cifrado de vault)
│   └── unresolved_nids.md       # ~20 avc_util_* pendientes de resolver
├── hardware/
│   ├── cell_side_channel.md     # lv0ldr key recovery via Cell SPE
│   ├── badwdsd.md               # BadWDSD + qCFW (Raspberry Pi Pico)
│   └── syscon.md                # SYSCON firmware key analysis
└── tools/
    └── termux_setup.md          # Entorno de investigación en Termux (mobile)
```

## Tools Used

- **radare2** 6.2.6 — Disassembly and analysis
- **capstone** 5.0.9 — Disassembly framework
- **keystone** (master) — Assembly framework (compiled from source with PR #600)
- **Ghidra** 12.2 — Decompilation
- **REA** v6.8.0 — Reverse Engineer Anything (MCP server)

## Related

- [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols) — PS3 NID database (contributed PR #27)
- [adri22235/ps4-14-00-security-notes](https://github.com/adri22235/ps4-14-00-security-notes) — PS4 14.00 research
- [adri22235/ps4-suid-scanner](https://github.com/adri22235/ps4-suid-scanner) — PS4 exploit framework

## Credits

- **zecoxao** (Jose Coixao) — sce_symbols maintainer, lv0ldr research lead, SYSCON key release
- **Shunsui** (H4SS9M) — PS4 kernel analysis, MP4 vulnerability discovery
- **@Modyfikator89** — BadWDSD hardware exploit
- **adri22235** (Adrián García Casado) — NID organization, DEH reference document, cross-firmware analysis

## License

MIT
