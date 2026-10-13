# be237_jb4

A static, offline-capable PS4 jailbreak host. It runs a WebKit exploit in the
console's browser, escalates through a kernel primitive, applies a per-firmware
kernel patch, and loads a payload (GoldHEN).

## Supported firmware

| Firmware | Status |
|----------|--------|
| 13.02 | Measured on hardware |
| 13.04 | Measured on hardware (same kernel as 13.02) |
| 13.50 | Measured on hardware |
| 13.52 | Measured on hardware (shares 13.50 kernel) |

Kernel RVAs are also present for 12.00, 12.02, 12.50, 12.52 and 13.00, but
only the firmwares above are treated as supported by the landing page.

## How it works

1. Open the host URL in the PS4 browser.
2. `index.html` detects the firmware from the user agent, shows whether it is in
   the kernel table, and (on the first run) caches every asset for offline use
   via AppCache.
3. It then redirects to `jb.html`, which loads `jb.js` and runs the exploit:
   WebKit stage -> kernel escalation -> per-firmware kpatch -> payload.
4. GoldHEN is loaded from `goldhen.bin`.

## USB fallback

If the network fetch of the payload fails, the host falls back to reading
`payload.bin` or `goldhen.bin` from the root of a USB drive (`/mnt/usb0`). This
is a safety net only: the normal HTTP path is unchanged and used first. Format
the drive as FAT32 and place `goldhen.bin` at its root before running the
jailbreak.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Firmware detection + AppCache bootstrap |
| `jb.html` / `jb.js` | Exploit runner |
| `ps4_offsets.js` | Per-firmware kernel RVAs, kpatch and payload names |
| `core.js`, `mem.js`, `int64.js`, `rpc_worker.js` | WebKit exploit helpers |
| `patches/*.bin` | Per-firmware kernel patches |
| `goldhen.bin` | Payload (GoldHEN) |
| `cache.appcache` | Offline cache manifest |

## Offline use

The whole host is cached on first load. Once cached, the jailbreak works with
the console offline. Bump the `# rev` line in `cache.appcache` (and the
matching SHA-256 comments) whenever a cached file changes so consoles pick up
the update.

## License

MIT — see [LICENSE](LICENSE).
