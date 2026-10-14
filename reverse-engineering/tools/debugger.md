# Debugger

**GDB** is a debugger that lets you run the executable, set breakpoints, step through instructions, and inspect registers/memory/state. It can debug programs at the source-code or assembly level.

## Useful Commands

- `gdb [program]` — open `[program]` in GDB
- `run`/`r` — start the program and stop at the first breakpoint
- `break [function/address]`/ `b [function/address]` — set a breakpoint before `[function/address]`
- `continue`/`c` — continue execution and stop at the next breakpoint
- `stepi`/`si` — execute one assembly instruction
- `finish` — run until the current function returns
- `disassemble [function]` — show assembly of `[function]`
- `info register [resgister]` — show `[register]`, if `[register]` is omitted, show all registers
- `quit`/`q` — quit GDB

## Memory Examination — `x`

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
