# PicoCTF Notes

This is a small note about my experience when solving CTF challenges.

## Web exploitation

### Standard/conventional files

- `index.html`, `style.css`, `script.js`
- `robots.txt` — gives instructions to web crawlers/bots, can reveal a path that the website doesn't link to directly.

### Cookies

**Cookies** are commonly used by websites to maintain state, including user authentication. They are stored on the client side (browser) and sent to the server with requests.

**Session cookies** can be security-critical. A session cookie is used by a web server to identify or maintain a user's session.

If a sensitive value such as a session ID/key, username, role, or authentication state is stored in the cookie without proper protection, it may be exploitable. Therefore, cookies can be valuable targets when they contain sensitive information or are improperly protected.

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

