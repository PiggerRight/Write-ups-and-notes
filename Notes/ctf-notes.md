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

### SSTI - Server Side Template Injection

**SSTI** occurs when user-controlled input is interpreted as server-side template code. In Jinja, this can allow an attacker to traverse accessible Python objects and potentially reach dangerous functionality such as OS interaction.

The primary defense is to keep user input as data rather than dynamically rendering it as a template, combined with appropriate sandboxing and least-privilege controls.

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

