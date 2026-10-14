### Entry Point 

An executable has an **entry point** (an address), and when the OS starts it, execution begins there. For a typical C program, this is usually `_start`, not `main`. (Entry point → runtime initializations → main → our program)

### Static and Dynamic Analysis

**Static Analysis** examines a program without executing it.

- Analyze assembly/machine code.
- Identify functions, instructions, control flow,...

**Dynamic Analysis** examines a program while it is running.

- Observe registers, memory, and program behavior.
- Step through instructions and inspect the program state.
- Tools: debugger(breakpoint),...

### Assembly

- `mov` — copies data from one location/register to another.
- `add`/`sub`/`imul` — addition/subtraction/multiplication; the result store in the first address/register.
- `cmp` — Compares two values by internally performing a subtraction and updating flags; does not store the result.
- `jle` — Jumps if the previous comparison indicates less than or equal (signed comparison).
- `jmp` — Unconditionally jumps to another instruction.
- `call` — Save the address of the next instruction (used by next `ret`), then jump to the called instruction (function).

### Disassemble

**Disassemble** is converting machine code into assembly instructions.

```bash
objdump [options] [program]
```

`[options]`

- `-M intel` — use Intel assembly syntax
- `-h` — header/section information
- `-d` — disassemble machine code (binary code)
- `-D` — disassemble all sections of the machine code, including non-code sections

Structure:

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

### Debugger (GDB)

**GDB** is a debugger that lets you run the executable, set breakpoints, step through instructions, and inspect registers/memory/state. It can debug programs at the source-code or assembly level.

**Useful Commands:**

- `gdb [program]` — open `[program]` in GDB
- `run`/`r` — start the program and stop at the first breakpoint
- `break [function/address]`/ `b [function/address]` — set a breakpoint before `[function/address]`
- `continue`/`c` — continue execution and stop at the next breakpoint
- `stepi`/`si` — execute one assembly instruction
- `finish` — run until the current function returns
- `disassemble [function]` — show assembly of `[function]`
- `info register [resgister]` — show `[register]`, if `[register]` is omitted, show all registers
- `quit`/`q` — quit GDB

**Memory Examination — `x`:**

Syntax:

```gdb
x/[number][format][unit] [address]
```

Example:

```text
x/4xb 0x1000
```

-    `4`   — `[number]`   → read 4 units
-    `x`   — `[format]`   → display in hexadecimal
-    `b`   — `[unit]`     → each unit is 1 byte
- `0x1000` — `[address]`  → starting memory address

So this command return data from `0x1000`,`0x1001`,`0x1002`,`0x1003` consecutively.

Memory addresses — `[address]` are commonly represented in hexadecimal. The address tells us where the data is stored, while the value tells us what is stored there.

```text
Address Data
0x1000  0x78
0x1001  0x56
```

Sometimes, in Assembly, we have this expression

`DWORD PTR [rbp-0x4]`.

It means:

```text
address = RBP - 0x4
DWORD → 4 bytes (32 bits)
[...] → access memory at the calculated address
```

So we can examine it directly in GDB:

```text
x/4xb $rbp-0x4
```

### Big Endian & Little Endian

**Endianness** is the order in which the bytes of a multi-byte value are stored in memory.

**Big Endian** stores the most significant byte first.

**Little Endian** stores the least significant byte first. Most modern x86/x86-64 systems use this and byte-addressable.

Example:
```text
Value: 0x12345678
Bytes: 12 34 56 78
Big Endian: Memory → 12 34 56 78
Little Endian: Memory → 78 56 34 12
```

### Unicode & UTF

**Unicode** is a standard that assigns a unique code point to each character.

**UTF** (Unicode Transformation Format) defines how Unicode code points are represented as bytes.

Example:

```text
慣 → U+6163 (Unicode)
       │
       ├── UTF-8 → E6 85 A3
       ├── UTF-16BE → 61 63
       └── UTF-16LE → 63 61

If you encounter strange Chinese-looking characters, they may not actually be Chinese text.
These characters could result from interpreting raw bytes as UTF-16BE.
```

1. **UTF-8**

   - **UTF-8** represents a Unicode character using 1-4 bytes.
   - **ASCII** character set is a subset of UTF-8 and uses exactly 1 byte for each character: `A → 41`, `a → 61`
   - UTF-8 does not have a Big Endian/Little Endian version.

2. **UTF-16**

   - **UTF-16** represents most common characters using 16-bit (2-byte) code units. Some Unicode characters require 4 bytes using a surrogate pair.
   - UTF-16 havs Big Endian/Little Endian versions.

In Python, each character have two forms: `str` and `bytes`:

- `encode()` converts `str` → `bytes` using a specific UTF.
- `decode()` converts `bytes` → `str` using a specific UTF.

