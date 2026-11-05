# APK_SHIELD
APK Shield

Check a suspicious Android app before you install it, and get a plain-language verdict in English or Tamil. 

The problem

Scam messages on WhatsApp and SMS ("RTO challan pending", "update your bank KYC", "PM scheme benefit") send people a link to an APK file outside the Play Store. Once installed, the app asks for SMS, notification and accessibility access, reads OTPs and can drain a bank account within minutes. The victims are often parents, shopkeepers and elderly people who cannot tell a fake app from a real one.

What APK Shield does

You choose an APK file. The page reads it, scores the risk from 0 to 100 and shows a clear verdict:

Score	Verdict
60 and above	DO NOT INSTALL
30 to 59	BE CAREFUL
Below 30	No major red flags found

Each verdict comes with short reasons in simple words, for example: "Can read your SMS messages, so it can steal OTPs." For high-risk apps it also shows the national cybercrime helpline (1930) and the reporting site, cybercrime.gov.in.

Features
Reads the APK directly in the browser. Nothing is uploaded, and the app is never installed or run.
English and Tamil, switchable at any time.
Detects dangerous permission combinations, such as SMS access plus screen control.
Flags apps that pretend to be a bank or government app (for example "SBI KYC Update").
Flags scam words in the app name (RTO, challan, KYC, PM Kisan, lottery and similar).
Reads the signing certificate and shows its SHA-256 fingerprint.
Flags apps signed with a test (debug) certificate, which genuine apps do not use.
Three built-in examples, so you can try it without an APK file.
Works on phones and desktops, in light and dark mode.
How it works
Unpack: the page reads the APK (a ZIP file) in the browser.
Read the manifest: it decodes the binary AndroidManifest.xml to get the package name, version, app name and requested permissions. Powers such as accessibility and notification access are declared on services, so those are read too.
Read the certificate: it extracts the signing certificate (v1, v2 and v3 signatures) and computes its fingerprint.
Score: a rule-based engine adds up the warning signs and picks the verdict.
Explain: the result is shown with reasons in the chosen language.
Scoring rules
Warning sign	Points
Can read SMS	30
Accessibility service (controls the screen)	30
Notification listener (reads OTP alerts)	20
Draw over other apps	15
Device admin	15
Can send SMS	12
Can install other apps	10
Can read contacts	8
SMS + accessibility together	+25
SMS + overlay together	+20
SMS + notification listener together	+15
Accessibility + overlay together	+10
Looks like a trusted bank or government app but is not	+45

Privacy
Your file is processed entirely inside your browser. No data is sent to any server.

Limitations

This is a prototype. Please read these before relying on it.

The result is a risk estimate, not a guarantee. A low score does not prove an app is safe.
Genuine banking and messaging apps also use SMS permissions, so they can score MEDIUM.
The trusted-app list has no real certificate fingerprints yet. The page can flag a fake "PhonePe" app, but it cannot confirm a genuine one.
The certificate is read but its signature is not verified.
It does not run or inspect the app's code, so apps that hide their behaviour or download it later can look cleaner than they are.
Very large APKs that use the ZIP64 format are not supported.
Checking a download link, a shared list of known scam apps and a Telegram bot are planned, not included in this version.
The Tamil text should be reviewed by a fluent reader.

Author

Sivanesan G, B.E. Computer Science and Engineering, St. Joseph's College of Engineering, Chennai.

Disclaimer

APK Shield is an educational project. Download apps only from the Google Play Store or the publisher's official website. If you lost money to an app scam, call 1930 and report at https://cybercrime.gov.in.
Scam words in the app name	+20
No signing certificate found	+15
Signed with a debug certificate	+15
