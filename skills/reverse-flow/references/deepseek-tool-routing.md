# DeepSeek / Agent Tool Routing for Windows EXE Analysis

## Purpose

Use the Windows EXE toolkit as a capability registry for an AI agent. The agent should select tools from evidence instead of blindly running every tool.

## Routing rules

### Intake

Input: `.exe`, `.dll`, or PE artifact.

First collect:

1. SHA-256
2. File size
3. PE architecture
4. PE sections and entropy
5. Imports/exports
6. TLS callbacks
7. Resources
8. Strings
9. Compiler/packer/protector hints

Preferred tools: `LIEF`, `pefile`, `Detect-It-Easy`, `YARA`, `capa`.

### Static native analysis

If the artifact is native x86/x64:

1. Open in Ghidra or radare2.
2. Identify entry point and module boundaries.
3. Recover imports, cross-references, functions and strings.
4. Use Zydis/Capstone for instruction-level queries.

### Managed code

If .NET is detected, route to a .NET-capable decompiler before native-only workflows.

### Suspected packing/protection

If section entropy, imports, entry point, signatures or runtime behavior indicate packing/protection:

1. Record the evidence.
2. Identify the suspected packer/protector.
3. Consult the curated packing catalogue.
4. Select a matching research/unpacking tool only when appropriate.
5. Preserve the original and work on a copy.

Do not assume that a protector name alone proves a particular implementation or version.

### Dynamic analysis

Use x64dbg/WinDbg when static analysis cannot establish runtime behavior. Record breakpoints, modules, relevant API observations, memory regions and timestamps.

### Emulation

Use Unicorn/Triton/angr only for isolated routines or paths where emulation/symbolic reasoning adds value. Do not execute unknown samples outside an isolated lab.

### Diffing

When two versions are available, use Diaphora/BinDiff to identify changed functions before doing deeper analysis.

## Tool result contract

Every adapter should return machine-readable JSON with this shape:

```json
{
  "tool": "tool-name",
  "version": "version-or-unknown",
  "input": {
    "path": "sample.exe",
    "sha256": "..."
  },
  "status": "ok",
  "findings": [],
  "artifacts": [],
  "limitations": [],
  "timestamp": "..."
}
```

The agent should never treat an empty result as proof that a feature is absent. Record `unknown` when a tool could not establish a fact.

## Agent loop

```text
user goal
  -> intake
  -> evidence collection
  -> hypothesis
  -> choose minimum useful tool set
  -> execute in isolated workspace
  -> normalize results
  -> update hypothesis
  -> choose next tool
  -> report verified facts + confidence + unknowns
```

## Important distinction

The toolkit registry does not grant an AI model the ability to execute local Windows programs. An actual desktop integration requires a local tool bridge/agent/MCP-style adapter or another host capability that exposes these tools. The repository therefore stores routing knowledge and adapters separately from the third-party binaries themselves.
