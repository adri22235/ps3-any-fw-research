# Termux Research Environment Setup

Mobile reverse engineering environment for PS3 research, running on Android via Termux.

## Installed Tools

### radare2 6.2.6
```bash
pkg install radare2
```
- Full disassembly framework
- `r2` for interactive analysis
- `rasm2` for inline assembly/disassembly
- `rabin2` for binary info extraction

### capstone 5.0.9
```bash
pkg install capstone
pip install capstone
```
- Disassembly framework (library)
- Python bindings for scripted analysis
- Supports x86, ARM, PPC, MIPS, and more

### keystone (compiled from master)
```bash
# PyPI package is OUTDATED — must compile from source
git clone https://github.com/keystone-engine/keystone.git
cd keystone
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release \
  -DLLVM_TARGETS_TO_BUILD="AArch64;X86" \
  -DBUILD_SHARED_LIBS=ON
make -j2
sudo make install

# Python bindings
cd ../bindings/python
python3 setup.py install
```

**Note**: PyPI package doesn't include PR #600 (CMake 4 + GCC 15 fix). Must compile from GitHub master.

### Verification
```bash
# radare2
rasm2 -a x86 -b 64 "mov rax, 0x1337"
# Expected: 48c7c037130000

# capstone (Python)
python3 -c "from capstone import *; print(Cs(CS_ARCH_X86, CS_MODE_64).version)"

# keystone (Python)
python3 -c "
from keystone import *
ks = Ks(KS_ARCH_X86, KS_MODE_64)
encoding, count = ks.asm('mov rax, 0x1337')
print(encoding, count)
"
# Expected: ([72, 199, 192, 55, 19, 0, 0], 1)
# Hex: 48 C7 C0 37 13 00 00
```

## Repository Access

### sce_symbols
```bash
git clone https://github.com/zecoxao/sce_symbols.git
```
Key files:
- `ps3_names.txt` — 8,141 function name → NID mappings
- `ps3.xml` — Structured NID database
- `ps3_deh_reference.md` — DEH reference (our PR #27)
- `nids.txt` — Additional NID listings
- `db.yml` — YAML NID database

### ps4norunpack
```bash
git clone https://github.com/zecoxao/ps4norunpack.git
```
PS4 package unpacker — cross-reference tool.

## Useful Commands

### NID Lookup
```bash
# Search for a function by name
grep -i "cellSsAimIsDEH" sce_symbols/ps3_names.txt

# Search by NID
grep "0x1F80E287" sce_symbols/ps3.xml

# Count total symbols
wc -l sce_symbols/ps3_names.txt
```

### Binary Analysis
```bash
# Disassemble a SELF file
r2 -a ppc -b 64 firmware.self
> aaa    # analyze all
> afl    # list functions
> pdf    # print disassembly of current function

# Quick disassemble hex bytes (PPC)
rasm2 -a ppc -b 64 -d "7c0802a6"

# Assemble PPC instruction
rasm2 -a ppc -b 64 "mflr r0"
```

### Session Management
```bash
# Prevent Android from killing Termux during long operations
termux-wake-lock

# Release when done
termux-wake-unlock
```

## Tips

1. Use `termux-wake-lock` before any compilation or long analysis session
2. `-j2` for make (most phones have limited RAM)
3. `proot-distro` if you need a full Linux environment
4. SSH server on port 8022: `sshd` → connect from PC for easier typing
5. `tmux` for persistent sessions that survive terminal close
