# Basic Concepts

## Static and Dynamic Analysis

**Static Analysis** examines a program without executing it.

- Analyze assembly/machine code.
- Identify functions, instructions, control flow,...

**Dynamic Analysis** examines a program while it is running.

- Observe registers, memory, and program behavior.
- Step through instructions and inspect the program state.
- Tools: debugger(breakpoint),...

## Big Endian & Little Endian

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

## Unicode & UTF

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
