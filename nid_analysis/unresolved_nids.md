# Unresolved NIDs

~20 entries in `ps3.xml` where the function name equals its own hash — meaning the real name is unknown.

## avc_util_* Family

These appear in the `avc_util` module and their names are unresolved:
- The "name" field contains the NID hash itself (e.g., `0xABCD1234`)
- Real function names unknown
- Module context suggests **Access Verification / Access Control** utilities

## Why They Matter

### AVC = Access Verification/Control
- `avc` in Linux/FreeBSD = Access Vector Cache (SELinux/MAC)
- PS3's `avc_util` likely manages security policy decisions
- These functions may control:
  - What code can access what resources
  - Permission checks for system calls
  - Security context transitions
  - Policy enforcement decisions

### Resolution Methods
1. **Dynamic analysis on DEH hardware**
   - Run code that calls these NIDs
   - Observe behavior and side effects
   - Cross-reference with known AVC patterns

2. **String analysis in firmware**
   - Search for debug strings near the function implementations
   - Error messages often contain function names
   - Assert macros may embed names

3. **Cross-reference with PSP/PSVita**
   - Similar NID resolution challenges existed
   - Some function families share patterns across Sony platforms
   - Vita NIDs sometimes reveal PS3 equivalents

4. **Brute-force NID resolution**
   - NIDs are SHA-1 hashes of function names
   - With a dictionary of likely function names, hashes can be matched
   - `avc_util_` prefix narrows the search space significantly

## Priority
**HIGH** — Access control functions are critical for understanding security boundaries.
If these functions implement MAC/DAC policies, they directly affect what exploits can and cannot do.

## Source
Entries from `ps3.xml` in [zecoxao/sce_symbols](https://github.com/zecoxao/sce_symbols) where `name` field matches the NID hash pattern.
