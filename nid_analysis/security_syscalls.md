# Security Syscalls

System calls related to security, authentication, DRM, and cryptography in PS3's LV2 kernel.

## Authentication

| Syscall | Function | Description |
|---------|----------|-------------|
| 8 | `sys_process_authenticate_program_segment` | Verifies SELF/SPRX digital signatures during loading. Core of the chain of trust. |

### Notes on syscall 8
- Called every time a signed executable (SELF) or library (SPRX) is loaded
- Verification chain: BootROM → lv0ldr → lv0 → lv1 → lv2 → SELF signature
- With lv0ldr keys recovered (Sep 2026), the root of this chain is compromised on ALL firmwares
- Patching this syscall = loading unsigned code

## NP DRM

| Syscall | Function | Description |
|---------|----------|-------------|
| 470 | `sys_npdrm_check_ekc` | Checks Encryption Key Chain for NP (PlayStation Network) content |
| 471 | `sys_npdrm_regist_ekc` | Registers new EKC entry |

### Notes
- EKC = Encryption Key Chain — manages licenses for digital purchases
- `check_ekc` verifies if a valid license exists for content
- `regist_ekc` adds new license entries
- Relevant for understanding digital content protection

## Crypto Engine

| Syscall | Function | Description |
|---------|----------|-------------|
| 976 | `sys_crypto_engine_create` | Creates a crypto engine context |
| 977 | `sys_crypto_engine_destroy` | Destroys crypto engine context |
| 978 | `sys_crypto_engine_hasher_prepare` | Prepares hashing operation |
| 979 | `sys_crypto_engine_hasher_run` | Executes hash computation |
| 980 | `sys_crypto_engine_hasher_get_hash` | Retrieves computed hash |
| 981 | `sys_crypto_engine_cipher_prepare` | Prepares encryption/decryption |
| 982 | `sys_crypto_engine_cipher_run` | Executes cipher operation |
| 983 | `sys_crypto_engine_cipher_finish` | Finalizes cipher, cleans up |
| 984 | `sys_crypto_engine_random_generate` | PRNG — generates random bytes |

### Architecture
```
Application
    │
    ▼
sys_crypto_engine_create (976)
    │
    ├── Hashing path:
    │   prepare (978) → run (979) → get_hash (980)
    │
    ├── Cipher path:
    │   prepare (981) → run (982) → finish (983)
    │
    ├── Random:
    │   random_generate (984)
    │
    ▼
sys_crypto_engine_destroy (977)
```

### Used For
- SELF file decryption during loading
- Save data encryption/decryption
- Trophy data protection
- Network communication encryption
- Content license verification

## LV2 SELF Decryption

| ID | Function | Description |
|----|----------|-------------|
| `0x00010042` | `proc_decrypt_lv2_self` | Decrypts the LV2 kernel SELF binary |

### Notes
- Not a numbered syscall — internal function
- Called during boot to decrypt the GameOS kernel
- Understanding this function = understanding kernel loading
- With lv0ldr keys, the decrypted LV2 can be analyzed directly

## Source
All syscalls verified in `ps3_names.txt` and `ps3.xml` from [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols).
