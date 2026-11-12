# Crypto Primitives (cellCryptoPu*)

51 functions providing low-level cryptographic operations. These are the building blocks used by higher-level security systems.

## Summary by Algorithm

| Algorithm | Operations | Key Sizes |
|-----------|-----------|-----------|
| **AES** | CBC, CFB-128, OMAC1 | 128/192/256-bit |
| **RSA** | Sign, Verify, Encrypt | 512/1024/2048-bit |
| **ECDSA** | Sign, Verify | Curve-specific |
| **ECDH** | Key Agreement | Curve-specific |
| **SHA** | Hash | SHA-1, SHA-256 |
| **HMAC** | MAC | SHA-1, SHA-256 |
| **TDES** | Encrypt/Decrypt | Triple DES (168-bit effective) |
| **PRNG** | Generate | FIPS-186 compliant |

## Detailed Breakdown

### AES (Advanced Encryption Standard)
- `cellCryptoPuAesCbcEncrypt` / `cellCryptoPuAesCbcDecrypt` — CBC mode
- `cellCryptoPuAesCfb128Encrypt` / `cellCryptoPuAesCfb128Decrypt` — CFB-128 mode
- `cellCryptoPuAesOmac1` — OMAC1 (One-key MAC, CMAC variant)

### RSA
- `cellCryptoPuRsa512Sign` / `cellCryptoPuRsa512Verify` / `cellCryptoPuRsa512Encrypt`
- `cellCryptoPuRsa1024Sign` / `cellCryptoPuRsa1024Verify` / `cellCryptoPuRsa1024Encrypt`
- `cellCryptoPuRsa2048Sign` / `cellCryptoPuRsa2048Verify` / `cellCryptoPuRsa2048Encrypt`

**Note**: RSA-512 is cryptographically broken. Its presence suggests legacy compatibility or non-critical uses.

### ECDSA (Elliptic Curve Digital Signature Algorithm)
- `cellCryptoPuEcdsaSign` — Generate signature
- `cellCryptoPuEcdsaVerify` — Verify signature

Used for SELF/SPRX authentication. The specific curves used by PS3 are research targets.

### ECDH (Elliptic Curve Diffie-Hellman)
- `cellCryptoPuEcdhKeyAgreement` — Key exchange

Used in secure communication channels (PSN, remote play).

### SHA (Secure Hash Algorithm)
- `cellCryptoPuSha1` — SHA-1 hash
- `cellCryptoPuSha256` — SHA-256 hash
- `cellCryptoPuHmacSha1` — HMAC-SHA-1
- `cellCryptoPuHmacSha256` — HMAC-SHA-256

### TDES (Triple DES)
- `cellCryptoPuTdesEncrypt` / `cellCryptoPuTdesDecrypt`

Legacy encryption. Likely used for backward compatibility with PS1/PS2 content.

### PRNG (Pseudo-Random Number Generator)
- `cellCryptoPuPrngGenerate` — FIPS-186 compliant random generation

## Research Relevance

### With lv0ldr Keys
- These functions can be called directly from custom code
- Key material used by these functions can be extracted
- The internal state of the crypto engine can be inspected
- Specific curves and parameters for ECDSA/ECDH can be identified

### Cross-Firmware
- These NIDs are stable across firmware versions
- The function signatures don't change
- Implementation may vary but interface is consistent
- Testing on one firmware reveals behavior on all

## Source
Function list from `ps3_names.txt` in [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols).
Full count: 51 functions confirmed.
