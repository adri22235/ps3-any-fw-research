# PS3 Any-Firmware Research Notes

## 1. lv0ldr Key Recovery

### Background
El lv0ldr (Level 0 Loader) es el primer código que ejecuta el Cell processor tras el BootROM. Controla la cadena de confianza completa: lv0 → lv1 → lv2 (GameOS kernel).

### Discovery (September 2026)
- **Vector**: Side-channel attack en Cell Broadband Engine (SPE)
- **Lead researchers**: zecoxao y colaboradores
- **Impact**: Recuperación de claves que permiten firmar código para lv0
- **Scope**: Aplica a TODOS los firmwares — las claves están fused en silicon

### Implications
- CFW posible en **PS3 Slim** (CECH-2xxx/3xxx) y **Super Slim** (CECH-4xxx)
- Estos modelos NO tenían CFW real hasta ahora (solo HEN/HAN)
- Las claves no pueden ser revocadas por actualización de firmware
- Abre investigación en todos los niveles del sistema

---

## 2. BadWDSD + qCFW

### Concept
Exploit hardware que utiliza un **Raspberry Pi Pico** para interactuar con la flash NOR de PS3 Slim/Super Slim.

### Technical Details
- **BadWDSD**: Manipulación del bus de datos de la flash NOR
- **qCFW**: Quick Custom Firmware — instalación de CFW via hardware
- **Creator**: [@Modyfikator89](https://twitter.com/Modyfikator89)
- **Unpatchable**: Opera a nivel de hardware flash, por debajo de cualquier protección de firmware

### Requirements
- Raspberry Pi Pico (~$4)
- Soldadura al punto de test de la placa (NOR flash)
- Conocimiento de layout de PCB específico por modelo

---

## 3. SYSCON Analysis

### What is SYSCON?
El SYSCON (System Controller) es un microcontrolador independiente en la placa de PS3 que gestiona:
- Power management
- Fan control
- Temperature monitoring
- Boot sequence initiation
- Communication con Cell BE

### Key Release
- zecoxao liberó la **clave de firmware SYSCON**
- Permite descifrar y analizar el firmware del controlador de sistema
- Aplica a todas las revisiones de hardware PS3

### Research Opportunities
- Análisis de la secuencia de boot a nivel hardware
- Entendimiento de protecciones pre-Cell
- Posibles vectores de persistencia

---

## 4. NID Resolution Status

### Resolved (from sce_symbols)
| Category | Count | Key Functions |
|----------|-------|---------------|
| DEH Detection | 11 | `cellSsAimIsDEH`, variants |
| Security Syscalls | 13 | syscall 8, 470-471, 976-984 |
| Vault/Storage | 3 | `cellSsVtrm{De,En}crypt` |
| Update Manager | 9 | `cellSsUm*` |
| Crypto Primitives | 51 | AES, RSA, ECDSA, SHA, TDES |

### Unresolved
- ~20 `avc_util_*` entries where name == hash
- Candidates for dynamic resolution on DEH hardware
- May reveal access control internals

### Method
All NIDs cross-referenced against `ps3_names.txt` (8,141 symbols) and `ps3.xml` in [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols).

---

## 5. Security Syscall Deep Dive

### Authentication (syscall 8)
```
sys_process_authenticate_program_segment
```
- Called during SELF/SPRX loading
- Verifies digital signature against keys in lv0/lv1
- With lv0ldr keys recovered, signatures can be forged on ANY firmware

### NP DRM (syscalls 470-471)
```
sys_npdrm_check_ekc    (470)
sys_npdrm_regist_ekc   (471)
```
- PlayStation Network DRM enforcement
- EKC = Encryption Key Chain
- Controls license verification for digital content

### Crypto Engine (syscalls 976-984)
```
sys_crypto_engine_create              (976)
sys_crypto_engine_destroy             (977)
sys_crypto_engine_hasher_prepare      (978)
sys_crypto_engine_hasher_run          (979)
sys_crypto_engine_hasher_get_hash     (980)
sys_crypto_engine_cipher_prepare      (981)
sys_crypto_engine_cipher_run          (982)
sys_crypto_engine_cipher_finish       (983)
sys_crypto_engine_random_generate     (984)
```
- Full crypto subsystem exposed via syscall interface
- Used internally for SELF decryption, save data, trophy encryption
- With lv0ldr keys, these can be instrumented para análisis

### LV2 SELF Decryption
```
proc_decrypt_lv2_self (0x00010042)
```
- Decrypts the LV2 kernel SELF binary
- Critical for understanding runtime kernel state

---

## 6. Cell SPE Security Architecture

### Overview
El Cell Broadband Engine tiene:
- 1x PPE (Power Processing Element) — kernel principal
- 7x SPE (Synergistic Processing Elements) — 6 disponibles para juegos, 1 reservado

### Security SPE (SPE isolation)
- Un SPE se ejecuta en **isolation mode**
- Código cargado y verificado por el BootROM
- Maneja operaciones criptográficas sensibles
- El side-channel de lv0ldr explota este SPE

### Research Vectors
- Timing attacks en operaciones criptográficas del SPE
- Power analysis (requiere hardware)
- Cache timing (posible desde PPE)
- Electromagnetic emanations (requiere equipo especializado)

---

## 7. Rebug D-REX on DECH-3000

### Context (November 2026)
- zecoxao demostró Rebug D-REX 4.84 corriendo en DECH-3000
- [Tweet](https://twitter.com/notnotzecoxao) — 12.4K views, 313 likes
- CC'd @ModernVintageG, @MrMario2011, @MODDED_WARFARE, @FilipeGomesPS

### Significance
- DECH-3000 = PS3 Debugging Station (hardware de desarrollo)
- Rebug D-REX = CFW con capacidades DEX
- Combinación permite acceso completo a funciones de desarrollo en hardware real
- Las funciones `cellSsAimIsDEH*` documentadas en nuestro PR #27 son las que detectan este hardware

---

## Timeline

| Date | Event |
|------|-------|
| Sep 2026 | lv0ldr key recovery via Cell side-channel |
| Sep 2026 | SYSCON firmware key released by zecoxao |
| Oct 2026 | BadWDSD + qCFW demonstrated by @Modyfikator89 |
| Nov 11, 2026 | Rebug D-REX 4.84 on DECH-3000 (zecoxao) |
| Nov 12, 2026 | PR #27 merged — DEH NID reference in sce_symbols |
| Nov 12, 2026 | This repository created |
