# APK Shield

**Check a suspicious app, message, QR code or Windows program before you trust it, and get a plain-language verdict in English or Tamil. Nothing is uploaded.**

> **Live demo:** `https://apkshield-eight.vercel.app/`

## The problem
Scam messages on WhatsApp and SMS ("RTO challan pending", "update your bank KYC", "PM scheme benefit") send people a link to an APK file outside the Play Store. Once installed, the app asks for SMS, notification and accessibility access, reads OTPs and can drain a bank account within minutes. The victims are often parents, shopkeepers and elderly people who cannot tell a fake app from a real one.

## What APK Shield does
You choose a file, paste a message or upload a QR photo. The page reads it, scores the risk from 0 to 100 and shows a clear verdict:

| Score | Verdict |
|---|---|
| 60 and above | **DO NOT INSTALL / NOT SAFE** (the whole screen turns red) |
| 30 to 59 | **BE CAREFUL** |
| Below 30 | No major red flags found |

Every verdict comes with short reasons in simple words, for example: "Can read your SMS messages, so it can steal OTPs." For high-risk results it also shows the national cybercrime helpline (**1930**) and the reporting site, cybercrime.gov.in.

## Four checkers on one page
| Tab | What it checks |
|---|---|
| **APK file** | Permissions, hidden icon, old target SDK, Telegram bot leaks, signing certificate, fake bank/government apps |
| **Message, link or QR** | Scam wording, fake bank-like domains, link shorteners, APK links, UPI payment QR codes |
| **Windows program (EXE)** | Misleading names (`invoice.pdf.exe`), packing, keylogging, ransomware signs, password stealing, coin mining |
| **Privacy and security** | Optional API keys, Tamil voice help, "Clear all my data" |

## Features
- Reads the file directly in the browser. **Nothing is uploaded**, and the app is never installed or run.
- **English and Tamil**, switchable at any time, with a **Listen** button that reads the verdict aloud (uses a Tamil voice installed on the device; a help card shows how to add one).
- **Malware report:** the score is split into threat types (for example "SMS and OTP stealer", "Banking trojan"), with the exact points for each, the likeness to known malware families, and what the malware would do to the user's phone or computer.
- **Red alert screen** for every HIGH result, and a note that 60 or more is not safe to use.
- Detects dangerous permission combinations, such as SMS access plus screen control.
- Flags apps that pretend to be a bank or government app. Impersonation is checked against real signing-certificate fingerprints for **PhonePe, Paytm, BHIM and DigiLocker**.
- Flags scam words in the app name (RTO, challan, KYC, PM Kisan, lottery and similar).
- Reads the signing certificate and shows its SHA-256 fingerprint; flags missing and debug certificates.
- Shows the file's **SHA-256** and a one-click **VirusTotal** link. Supports files up to **1 GB**.
- **QR codes:** reads a QR photo, including tilted or noisy ones, and checks the link or UPI request inside.
- **Optional online checks** with the user's own VirusTotal and Google Safe Browsing keys (only a file hash or link text is sent).
- Built-in examples on every tab, so it can be tried without any file.
- Works on phones and desktops, in light and dark mode.

## How it works
1. **Unpack:** the page reads the file (an APK is a ZIP) in the browser, streaming large files.
2. **Read the manifest:** it decodes the binary AndroidManifest.xml to get the package name, app name, target SDK, requested permissions, and the services that hold powers such as accessibility and notification access.
3. **Read the certificate:** it extracts the signing certificate (v1, v2 and v3 signatures) and computes its fingerprint.
4. **Score:** a rule-based engine adds up the warning signs and picks the verdict. Each point belongs to one threat type, so the type points add up exactly to the total.
5. **Explain:** the result is shown with reasons in the chosen language, plus the effects and next steps.

Windows programs are read with a built-in PE reader (headers, sections, imports, entropy, strings); QR codes are decoded with the browser's BarcodeDetector or the bundled jsQR library.

## Scoring rules (APK)
| Warning sign | Points |
|---|---|
| Can read SMS | 30 |
| Accessibility service (controls the screen) | 30 |
| Notification listener (reads OTP alerts) | 20 |
| Draw over other apps | 15 |
| Device admin | 15 |
| Can send SMS | 12 |
| Can install other apps | 10 |
| Can read contacts | 8 |
| SMS + accessibility together | +25 |
| SMS + overlay together | +20 |
| SMS + notification listener together | +15 |
| Accessibility + overlay together | +10 |
| Genuine package name but a different certificate (fake PhonePe, Paytm, BHIM, DigiLocker) | +60 |
| Looks like a trusted bank or government app but is not | +45 |
| Trusted package name, certificate not on our list | +20 |
| Sends data to a Telegram bot (with sensitive permissions) | +30 |
| Scam words in the app name | +20 |
| Hidden launcher icon (with sensitive permissions) | +15 |
| No signing certificate found | +15 |
| Signed with a debug certificate | +15 |
| Very old target SDK (with sensitive permissions) | +10 |

Totals are capped at 100. A confirmed genuine app (certificate matches) is capped at 10. The message, QR and Windows-program rules are listed in [docs/TECHNICAL_DOCUMENTATION.md](docs/TECHNICAL_DOCUMENTATION.md).

## Setup
1. Download or clone this repository.
2. Open **`index.html`** in Chrome, Edge or Firefox. That is the whole app; nothing to install.

Or serve it: `python3 -m http.server 8000` and open http://localhost:8000.
To publish it, drag the folder onto Vercel or Netlify (the included `vercel.json` / `_headers` add security headers). The readable source is `src/apk-shield.html`; after editing it run `python3 build.py`.

## Tests
```bash
pip install playwright && playwright install chromium
python3 tests/run_all.py
```
Four headless-browser suites cover APK and message checks (10 test APKs), Windows programs (18 files), QR codes (8 images) and security (mocked VirusTotal and Safe Browsing, data wipe, script injection, a content-security-policy run). `tests/large_test.py` checks a 1 GB file. The optional server has 92 unit tests.

## Privacy and security
Your file is processed entirely inside your browser. No data is sent to any server unless you add your own API key, and then only a file's SHA-256 or a link's text is sent. The page has a hash-locked Content Security Policy, no third-party requests and no tracking, shows hostile file names as plain text, keeps keys for the tab only by default, and has a **Clear all my data** button. Details: [SECURITY.md](SECURITY.md).

## Limitations
This is a prototype. Please read these before relying on it.
- The result is a **risk estimate, not a guarantee.** A low score does not prove an app is safe, and genuine banking and messaging apps also use SMS permissions, so they can score MEDIUM.
- Certificate fingerprints exist for **four** apps (PhonePe, Paytm, BHIM, DigiLocker). The page can flag a fake version of other apps, but cannot confirm a genuine one.
- The certificate is read but its signature is not verified (same for Windows programs: "signed" is detected, not verified).
- It does not run the app's code, so apps that hide their behaviour or download it later can look cleaner than they are.
- Tested on files we generated ourselves, not on real malware. Malware family names are behaviour matches, not lab identifications.
- A QR code needs a reasonably clear image. Packed but legitimate Windows software can be flagged. For files over 128 MB only the first and last 64 MB are string-scanned.
- Very large APKs that use the ZIP64 format are not supported.
- The optional server version (FastAPI + Telegram bot, in `server/`) has not yet been upgraded with the newer rules.
- The Tamil text should be reviewed by a fluent reader, and the voice depends on the device.

## Roadmap
Upgrade the server and Telegram bot to the same rules · WhatsApp/SMS forwarding bot · more trusted apps and a shared list of known scam certificates · installable offline app (PWA) · more Indian languages · pilots with colleges and local banks.

## Author
**Sivanesan G**, B.E. Computer Science and Engineering, St. Joseph's College of Engineering, Chennai.

## Disclaimer
APK Shield is an educational project. Download apps only from the Google Play Store or the publisher's official website. If you lost money to an app scam, call **1930** and report at https://cybercrime.gov.in.
