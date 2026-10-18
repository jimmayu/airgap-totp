# airgap-totp

**Demo (GitHub Pages):** https://jimmayu.github.io/airgap-totp/

**Secure usage:** Save the `index.html` file from this repository and open it locally (e.g., `file:///path/to/index.html` or via a trusted local static server). Do **not** enter a real shared secret into the hosted demo.

---

## GitHub Pages

The demo above is this same `index.html`, published from the `gh-pages` branch. GitHub hosts that static file for free. The hosted copy is not air-gapped: it is a convenience so you can see the page. Do not type a real shared secret into it.

## What is this?

A single, self‑contained HTML file that implements an offline TOTP authenticator.

- Implements RFC 6238 (TOTP) and RFC 4226 (HOTP) core algorithm.
- Uses HMAC‑SHA1, 6‑digit codes, 30‑second time step.
- No external dependencies. The file itself performs no network requests after it loads. A host, CDN, or browser extension can still add one; that is outside this file.

## Who is it for?

Anyone who needs to generate a TOTP code from a shared secret **without** trusting a phone app, browser extension, password manager, or any remote server. It is designed for users who want to audit the entire program before entering a secret.

## Who is it **not** for?

- Daily‑driver authenticator expecting convenience features.
- SHA-256, SHA-512, 8-digit codes, or a period other than 30 seconds.
- Steam codes, backup codes, or a multi-account vault. Google Authenticator setup strings that are standard `otpauth://totp/` URIs with SHA-1, 6 digits, and a 30-second step do work. Anything else is rejected.

## How to use

1. **Save** `index.html` from this repository to your computer.
2. Open the saved file in a browser. Double-clicking it uses a `file://` URL. A local static server you already trust is also fine. The copy button can fail on `file://` in some browsers; if it does, select the code and copy it yourself.
3. Paste either a Base32‑encoded secret or an `otpauth://totp/...` URI into the *Shared secret* field.
4. The current 6‑digit code appears and updates every 30 seconds. The remaining seconds are shown next to the code.
5. Click **Copy code** to copy the current code to the clipboard.

### Supported input formats

- **Base32** secret (case‑insensitive, spaces and dashes are ignored, padding optional).
- **otpauth://totp/...** URI where:
  - `algorithm` may be omitted or must be `SHA1`.
  - `digits` may be omitted or must be `6`.
  - `period` may be omitted or must be `30`.

The secret must decode to **10–64 bytes**.

## How to use it securely (required)

- **Prefer the saved file** over the GitHub Pages demo. The demo runs on GitHub’s servers and can be changed at any time; a malicious copy could exfiltrate your secret.
- **Read the script** before entering any secret. In `index.html`, confirm the `<meta http-equiv="Content-Security-Policy">` tag sets `default-src 'none'` and `connect-src 'none'`. GitHub Pages will not send that policy as an HTTP response header; the tag in the file is the control. The page contains no `fetch`, `XMLHttpRequest`, WebSocket, storage, or cookie usage.
- **No persistence**: the secret is held only in memory. Reloading the page clears it. The secret is never placed in the URL, cookies, `localStorage`, `sessionStorage`, or IndexedDB.
- **Clipboard handling**: the copy button creates a temporary `<textarea>` and uses `document.execCommand('copy')`. The code is placed on the system clipboard; clear it after pasting if you are concerned about clipboard snooping.
- **No previous/next codes** are displayed, reducing the attack surface.
- **Clock accuracy**: TOTP depends on the device clock. The page does **not** synchronize time over the network.
- **Self‑test**: on load the page runs a SHA‑1 self‑test and RFC 6238 test vectors. If this test fails, the input is disabled and a warning is shown.
- **Engine selection**:
  - If the browser supports the Web Crypto API and its self‑test passes, the page uses `Web Crypto HMAC‑SHA1`.
  - Otherwise it falls back to an in‑page JavaScript implementation of HMAC‑SHA1.

## Project layout

- `index.html` – the only executable component of the authenticator.
- `README.md` – this documentation.
- `SECURITY.md` – threat model and non‑goals.
- `LICENSE` – CC0‑1.0 waiver.

The extra files are **not** loaded by the page.

## Security details

See [SECURITY.md](SECURITY.md) for the full threat model, scope, and disclosure process.

---

*Repository:* https://github.com/jimmayu/airgap-totp
