# Windows EXE Reverse Engineering Toolkit

> Curated tool registry for Windows PE/EXE/DLL reverse engineering. This file records upstream projects and recommended roles; it does not vendor third-party source code into this repository.

## 1. Core static analysis

| Tool | Upstream | Role | Priority |
|---|---|---|---|
| Ghidra | https://github.com/NationalSecurityAgency/ghidra | Disassembly, decompilation, function/data-flow analysis | P0 |
| radare2 | https://github.com/radareorg/radare2 | Scriptable binary analysis, disassembly and debugging | P0 |
| RetDec | https://github.com/avast/retdec | Native binary decompilation | P1 |
| Reko | https://github.com/uxmal/reko | Native binary decompiler | P1 |

## 2. Windows debugging

| Tool | Upstream | Role | Priority |
|---|---|---|---|
| x64dbg | https://github.com/x64dbg/x64dbg | x86/x64 user-mode debugging | P0 |
| WinDbg | https://learn.microsoft.com/windows-hardware/drivers/debugger/ | Windows native debugging | P1 |
| ScyllaHide | https://github.com/x64dbg/ScyllaHide | Anti-debug research/testing plugin framework | P1 |

## 3. PE parsing and instruction libraries

| Library | Upstream | Role | Priority |
|---|---|---|---|
| LIEF | https://github.com/lief-project/LIEF | PE/ELF/Mach-O parsing and binary manipulation | P0 |
| pefile | https://github.com/erocarrera/pefile | Python PE parsing | P0 |
| Zydis | https://github.com/zyantific/zydis | High-performance x86/x64 instruction decoder/disassembler | P0 |
| Capstone | https://github.com/capstone-engine/capstone | Multi-architecture disassembly | P1 |
| Keystone | https://github.com/keystone-engine/keystone | Multi-architecture assembly/encoding | P2 |

## 4. Emulation and program analysis

| Tool | Upstream | Role | Priority |
|---|---|---|---|
| Unicorn | https://github.com/unicorn-engine/unicorn | CPU emulation | P1 |
| Triton | https://github.com/JonathanSalwan/Triton | Dynamic binary analysis and symbolic execution | P1 |
| angr | https://github.com/angr/angr | Binary analysis and symbolic execution | P1 |
| unicorn_pe | https://github.com/hzqst/unicorn_pe | PE-oriented emulation/research workflows | P1 |

## 5. Packing / unpacking research

| Project | Upstream | Role | Priority |
|---|---|---|---|
| Unlicense | https://github.com/ergrelet/unlicense | Research tooling for Themida/WinLicense protected binaries | P1 |
| UnpackThemida | https://github.com/TopSoftdeveloper/UnpackThemida | Themida/WinLicense research | P1 |
| Unipacker | https://github.com/unipacker/unipacker | Automated PE unpacking research | P1 |
| awesome-executable-packing | https://github.com/packing-box/awesome-executable-packing | Curated catalogue of packers, protectors, unpacking tools and research | P0 |

## 6. Binary diffing

| Tool | Upstream | Role | Priority |
|---|---|---|---|
| Diaphora | https://github.com/joxeankoret/diaphora | Function-level binary diffing and similarity analysis | P0 |
| BinDiff | https://github.com/google/bindiff | Binary similarity/diffing | P1 |

## 7. Detection / signatures

| Tool | Upstream | Role | Priority |
|---|---|---|---|
| Detect It Easy | https://github.com/horsicq/Detect-It-Easy | File type, compiler and packer/protector identification | P0 |
| YARA | https://github.com/VirusTotal/yara | Signature-based classification and hunting | P0 |
| capa | https://github.com/mandiant/capa | Identify capabilities from executable features | P1 |

## 8. Recommended analysis pipeline

```text
EXE/DLL
  -> SHA-256 + file metadata
  -> PE parsing (LIEF/pefile)
  -> architecture/sections/imports/exports/TLS/resources
  -> entropy + packer/protector detection (DIE/YARA/capa)
  -> strings and references
  -> static analysis (Ghidra/radare2)
  -> instruction-level analysis (Zydis/Capstone)
  -> controlled debugging (x64dbg/WinDbg)
  -> emulation/symbolic analysis when justified (Unicorn/Triton/angr)
  -> binary diffing when multiple versions exist (Diaphora/BinDiff)
  -> evidence-backed report
```

## 9. Agent integration model

The reverse-flow agent should treat each tool as a capability, not as a mandatory step. Tool selection should be evidence-driven:

- PE metadata/import anomaly -> LIEF/pefile
- suspected packer/protector -> DIE/YARA/capa, then targeted research tooling
- native code recovery -> Ghidra/radare2/Zydis
- runtime behavior -> x64dbg/WinDbg
- isolated instruction/path reasoning -> Unicorn/Triton/angr
- version comparison -> Diaphora/BinDiff

Every tool invocation should record: tool name, version, command/configuration, input hash, output path, timestamp, and confidence/limitations.

## 10. Scope

This registry is intended for authorized reverse engineering, CTF/crackme, malware-analysis labs, interoperability research, and defensive auditing. It is a catalogue and orchestration reference; third-party projects remain under their respective upstream licenses.
