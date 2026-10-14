# Assembly

Assembly language is a low-level programming language that represents machine instructions using human-readable mnemonics. It is architecture-dependent, meaning different CPU architectures (e.g., x86-64 and ARM) use different instruction sets.

## Basic Concepts

- **Registers:** Small, fast storage locations inside the CPU, e.g., `rax`, `rbx`, `rsp`, `rbp`.
- **Instructions:** Operations executed by the CPU, such as `mov`, `add`, `sub`, `cmp`, `jmp`, `call`, and `ret`.
- **Operands:** Values or locations an instruction operates on, such as registers, constants, and memory addresses.
- **Memory:** Stores program instructions and data. Assembly accesses memory through addresses, often using registers.

## Common Instructions (x86-64)

- `mov` — copies data from one location/register to another.
- `add`/`sub`/`imul` — addition/subtraction/multiplication; the result store in the first address/register.
- `cmp` — Compares two values by internally performing a subtraction and updating flags; does not store the result.
- `jle` — Jumps if the previous comparison indicates less than or equal (signed comparison).
- `jmp` — Unconditionally jumps to another instruction.
- `call` — Save the address of the next instruction (used by next `ret`), then jump to the called instruction (function).

## Example

```asm
mov eax, 5
mov ebx, 3
add eax, ebx
```

After execution, `eax = 8`.

## Why Assembly Matters in RE

Assembly helps us understand how compiled programs actually execute. In reverse engineering, we analyze instructions, registers, memory, and control flow to reconstruct program logic without relying on the original source code.
