# Binary Inspection

These tools help inspect a binary before debugging or reverse engineering it. They provide information about its file type, readable strings, ELF structure, and assembly instructions.

## `file`

Identifies the file format, architecture, and other basic properties.

```bash
file [options] [files...]
```

## `strings`

Extracts sequences of printable characters from a binary.

```bash
strings [options] [files...]
```

`[options]`

- `-n [n]` — only display strings with at least `[n]` printable characters, default is 4.
- `-a` — scan the entire file, mostly default on many Linux system.

Note: Strings can be absent, encoded, or constructed at runtime.

## `readelf`

Displays information about ELF (Executable and Linkable Format) files, commonly used by Linux executables and shared libraries.

```bash
readelf [options] [file]
```

`[options]`

- `-h` — ELF header: architecture, file type, entry point
- `-S` — section headers, such as `.text` and `.data`
- `-l` — program headers and loadable segments
- `-s` — symbol table, if available

## `objdump`

**Disassemble** is converting machine code into assembly instructions. Displays low-level information about object files and executables, including assembly instructions.

```bash
objdump [options] [file]
```

`[options]`

- `-M intel` — use Intel assembly syntax
- `-h` — header/section information
- `-d` — disassemble machine code (binary code)
- `-D` — disassemble all sections of the machine code, including non-code sections

### Structure

```asm
08049146 <main>:
├── Address / label
│       ↓
│   08049146 <main>
│
└── Instructions
    ├── 8049146:    55          push   %ebp
    │                │            │       │
    │                │            │       └── operand
    │                │            └── instruction
    └── address      └── machine-code bytes 
```

```text
ELF
 │
 ├── Header ───────────────→ What is this file?
 │
 ├── Program Headers ──────→ How should OS load it?
 │
 ├── Sections ──────────────→ What is inside it?
 │      │
 │      ├── .text ──────────→ Code
 │      ├── .rodata ────────→ Constants/strings
 │      ├── .data ──────────→ Initialized data
 │      ├── .bss ───────────→ Uninitialized data
 │      └── ...
 │
 └── Section Headers ───────→ Where/how are sections described?
```

### Entry Point 

An executable has an **entry point** (an address), and when the OS starts it, execution begins there. For a typical C program, this is usually `_start`, not `main`. (Entry point → runtime initializations → main → our program)
