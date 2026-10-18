# Security Model and Non‑Goals for airgap‑totp

## Asset

- **Primary asset:** the shared secret entered by the user.
- **Secondary asset:** the current 6-digit code. It is shorter-lived than the secret, and still enough to log in until the step ends.

## In‑Scope

- The **web page** (`index.html`) after it has been loaded in a trusted browser tab.
- The page must **not transmit** the secret over the network, store it in cookies, `localStorage`, `sessionStorage`, IndexedDB, or embed it in the URL.
- The Content-Security-Policy is a `<meta http-equiv>` tag in `index.html`: `default-src 'none'` and `connect-src 'none'`. It is not an HTTP response header. It forbids network requests, images, fonts, and form submission from the page.

## Out‑of‑Scope

- The **delivery channel** of `index.html` (GitHub, a USB stick, email, etc.). A compromised copy defeats the design.
- GitHub Pages TLS and the security of the GitHub account hosting the demo; the demo is provided for convenience only and is not air‑gapped.
- The underlying operating system, hardware, or browser implementation beyond the page’s CSP.

## Trusted Computing Base

- The **browser** executing the page is trusted for the duration of the tab. Browser extensions, developer tools, compromised renderer processes, or OS‑level clipboard managers can still read the secret or the generated code.
- No persistence is performed by design: closing the tab discards the secret from memory. This does **not** guarantee that the secret is removed from process memory, swap, or crash dumps.

## Supported Algorithm and Validation

- Only **HMAC‑SHA1**, **6‑digit codes**, **30‑second step** are accepted.
- The page **rejects** otpauth URIs that specify a different algorithm, digit count, or period, returning an error message.
- HOTP‑only URIs are rejected.
- The secret must decode to between **10 and 64 bytes** after Base32 decoding; otherwise an error is shown.

## Threats Considered

| Threat | Mitigation |
|--------|------------|
| Secret exfiltration via network request | CSP `connect-src 'none'` and no fetch/XHR/WS calls. |
| Secret persisted on disk | No use of cookies, storage APIs, or URL parameters. |
| Secret leaked via clipboard after copy | The copy operation is explicit; user can clear clipboard afterward. |
| Malicious version of `index.html` | Users are instructed to verify the script (CSP header, absence of network calls) and to use a saved copy from a trusted source. |
| Timing side‑channel on the browser | Acceptable given the threat model; not mitigated. |

## Disclosure Process

If you discover a network request, storage of the secret, or an incorrect RFC test vector, please open an issue on the GitHub repository:

https://github.com/jimmayu/airgap-totp/issues

Do **not** include the real shared secret in the issue description.

## No Formal Audit

No security audit has been performed. The design relies on simplicity and the property that the entire source is visible to the user.

---

*Repository:* https://github.com/jimmayu/airgap-totp
