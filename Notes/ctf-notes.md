# PicoCTF Notes

This is a small note about my experience when solving CTF challenges.

## Web exploitation

### Front-end

- Standard/conventional files
    - `index.html`, `style.css`, `script.js`
    - `robots.txt` — gives instructions to web crawlers/bots, can reveal a path that the website doesn't link to directly.
    - Back-up files
 
- **Frontend** validation can be useful for UX, but it cannot be the security boundary. The **Backend** must enforce the security.

### Back-end

- PHP is a server-side scripting language that can execute code on a web server.
    - PHP type juggling of dynamically typed language → enforce the expected type before a security-sensitive comparison.
    - Return of `sha1()`, `md5()`,...

- Users should follow the principle of least privilege and should not be given unnecessary or unrestricted permissions.

- Manage API calling properly.

- A blocklist cannot stop code injection, because every language offers several ways to express the same thing: concatenation, `chr()`, and `__import__()` rebuild any banned token at runtime without it ever appearing in the input. The safe answer is to keep user input away from `eval` and `exec` entirely. Where dynamic evaluation is genuinely needed, allowlist the exact operations the feature requires.

### File upload

**File uploads** should strictly validate file types and file contents, and uploaded files should not be executable as server-side code.

### SSTI — Server Side Template Injection

**SSTI** occurs when user-controlled input is interpreted as server-side template code. In Jinja, this can allow an attacker to traverse accessible Python objects and potentially reach dangerous functionality such as OS interaction (RCE).

The primary defense is to keep user input as data rather than dynamically rendering it as a template, combined with appropriate sandboxing and least-privilege controls.

### NoSQL Injection

**NoSQL Injection** is an injection attack against a NoSQL database, similar in concept to SQL injection but targeting databases such as MongoDB, Redis, CouchDB, etc. It occurs when user input can modify the structure or operators of a NoSQL database query.

Carefully validate and sanitize user input before using it to construct queries. Do not allow user-controlled input to modify the query structure.

### Cookies

**Cookies** are commonly used by websites to maintain state, including user authentication. They are stored on the client side (browser) and sent to the server with requests.

**Session cookies** can be security-critical. A session cookie is used by a web server to identify or maintain a user's session.

If a sensitive value such as a session ID/key, username, role, or authentication state is stored in the cookie without proper protection, it may be exploitable. Therefore, cookies can be valuable targets when they contain sensitive information or are improperly protected.

### Authentication

- Never trust the client to enforce authentication. Authentication and authorization checks must be enforced server-side.
- Security-sensitive fields must be properly validated on the server, including explicitly rejecting missing or invalid authentication data.

### Burp Suite

**Burp Suite** is a platform for web application security testing. It acts as an intermediary between the client (browser) and the web server, allowing us to inspect and modify HTTP requests and responses. This makes it useful for discovering flaws in server-side request handling, authentication, and input validation.

1. Proxy

**Proxy** intercepts HTTP requests and responses between the browser and the server. It can be used to:

- Inspect requests and responses.
- View cookies, headers, parameters, and request bodies.
- Modify requests before forwarding them to the server.

2. Repeater

**Repeater** allows us to manually modify and resend a captured request multiple times. It is useful for:

- Testing different parameter values.
- Modifying headers or cookies.
- Testing how the server handles malformed or unexpected requests.
- Comparing different server responses.

3. Intruder

**Intruder** allows us to automatically send a large number of requests with different payloads. It is useful for:

- Testing different parameter values automatically.
- Testing input fields with a list of payloads (Bruteforce).
- Finding valid or interesting values by comparing server responses.

## Reverse Engineering

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

**Disassemble** is converting machine-code bytes into assembly instructions.

```bash
objdump -d [options] ./program
```

`[options]`

- `-M intel` — use Intel assembly syntax

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

```bash
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

## Forensics

### SVG

**SVG (Scalable Vector Graphics)** is an image format based on **XML**. Unlike raster images such as PNG and JPG, an SVG describes an image using **text-based** elements such as shapes, paths, and attributes.

Therefore, they can contain additional text or data, making them worth inspecting in CTF challenges. Unlike PNG/JPG, SVG graphics can be scaled without losing quality.

Example:
```xml
<svg>
    <circle cx="50" cy="50" r="20" fill="red"/>
</svg>
```

