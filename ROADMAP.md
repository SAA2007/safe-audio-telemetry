# Project S.A.F.E. - Development Roadmap

**Goal:** Build a fault-tolerant, encrypted audio logger for personal devices.
**Stack:** Android (Java/Kotlin) + Python (FastAPI) + Opus Codec
**Context:** Personal security/telemetry. Resume-grade project.

---

## 🟢 PHASE 0: THE FOUNDATION
**Goal:** Set up the workspace and legal "paper trail."
**Estimated Difficulty:** 1/10
**Estimated Time:** 1 Day

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [x] Create GitHub Repository (Name: `safe-audio-telemetry`).
- [x] Create Folder Structure:
    - `/android-app` (Mobile code)
    - `/python-server` (Backend code)
    - `/docs` (Notes)
- [x] Write `README.md`:
    - *Action:* State clearly: "A store-and-forward telemetry system for devices I own, focusing on resilience in poor network conditions."
- [x] Write `ETHICS.md`:
    - *Action:* "This software requires a visible foreground notification to run. It is designed for personal data logging, not surveillance."

> **💡 Senior Dev Tip:** Future employers check the commit history. Don't upload "test" keys or passwords to GitHub. Use a `.gitignore` file immediately.

---

## ⚪ PHASE 1: THE SERVER BACKBONE (Python)
**Goal:** A server that stays alive and accepts a "handshake".
**Estimated Difficulty:** 3/10
**Estimated Time:** 1 Week

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Install VS Code + Python 3.10+.
- [ ] Install FastAPI: `pip install "fastapi[standard]"`
- [ ] Create `server.py`.
- [ ] Write a WebSocket endpoint (`/ws/connect`).
- [ ] Implement the "Handshake":
    - Client sends: `{"id": "pixel7", "status": "hello"}`
    - Server replies: `{"status": "welcome"}`
- [ ] Test it with a simple Python script (not the phone yet).

> **💡 Senior Dev Tip:** You strictly want `WebSockets` (TCP), not UDP. Why? Because you need to know if the data arrived. UDP is "fire and forget" (bad for logging). TCP is "guaranteed delivery."

---

## ⚪ PHASE 2: ANDROID BASICS (The Hardest Part)
**Goal:** An app that runs 24/7 without the OS killing it.
**Estimated Difficulty:** 8/10
**Estimated Time:** 2-3 Weeks

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Create Android Project (Empty Views Activity).
- [ ] Create a "Foreground Service" class.
    - *Requirement:* It MUST show a notification. Title: "Telemetry Active".
    - *Tip:* Set notification priority to `MIN` so it doesn't make sound or pop up.
- [ ] Add "Boot Receiver":
    - *Action:* Make the app restart automatically if the phone reboots.
- [ ] Handle "Battery Optimization" (The missing piece):
    - *Action:* Add code to request `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS`.
    - *Why:* Samsung phones will kill your app after 3 days if you don't do this.
- [ ] Connect Android to your Python server via WebSocket.

> **💡 Senior Dev Tip:** Use the library `OkHttp` for WebSockets. It handles the difficult networking stuff for you.

---

## ⚪ PHASE 3: AUDIO CAPTURE (Local Only)
**Goal:** Record clear audio to a file.
**Estimated Difficulty:** 4/10
**Estimated Time:** 1 Week

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Add Microphone Permission to `AndroidManifest.xml`.
- [ ] Create a "Consent Screen" on first launch.
    - *User must tap "I Agree" to enable the mic. Save this to settings.*
- [ ] Implement `AudioRecord`:
    - *Config:* 16,000Hz, Mono, 16-bit PCM.
- [ ] Save raw audio to a `.wav` file on the phone storage.
- [ ] Test: Record 1 hour. Play it back on PC.

---

## ⚪ PHASE 4: COMPRESSION (Opus Codec)
**Goal:** Shrink 100MB of audio down to 5MB.
**Estimated Difficulty:** 6/10
**Estimated Time:** 1-2 Weeks

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Add an Opus library to Android (search `android-opus-codec` on GitHub).
- [ ] Change pipeline: Mic -> PCM -> Opus Encoder -> File.
- [ ] Target: 8kbps (VOIP quality).
- [ ] Compare file sizes. You should see a 10x-20x reduction.

---

## ⚪ PHASE 5: THE "STORE-AND-FORWARD" ENGINE
**Goal:** Reliability. If internet fails, data waits in a queue.
**Estimated Difficulty:** 9/10
**Estimated Time:** 3 Weeks

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] "Chunking":
    - Don't record one huge file. Record 2-minute clips.
    - Name them: `1701234000_chunk.opus` (Use timestamps).
- [ ] "The Queue":
    - Save chunks to a local folder `/pending_uploads`.
- [ ] "The Worker" (Background Thread):
    - Loop:
        1. Is Internet available?
        2. No? Sleep 10s.
        3. Yes? Pick oldest file.
        4. Send to Server.
        5. Did Server reply "ACK" (Acknowledged)?
        6. Yes? Delete local file.
        7. No? Keep file, try again later.

> **💡 Senior Dev Tip:** This "ACK" logic is critical. Never delete the file from the phone until the server confirms it has saved it. This guarantees zero data loss.

---

## ⚪ PHASE 6: SECURITY (Pairing & Keys)
**Goal:** No Passwords. Only Keys.
**Estimated Difficulty:** 7/10
**Estimated Time:** 2 Weeks

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Client Generates Keypair (Public/Private) on first run.
- [ ] Display "Public Key Fingerprint" on the phone screen.
- [ ] Server Whitelist:
    - Create a file `allowed_clients.json` on server.
    - Manually paste your phone's Public Key there.
- [ ] Authentication:
    - Phone signs a message with Private Key.
    - Server verifies with Public Key.
    - If valid -> Accept Audio.

---

## ⚪ PHASE 7: ENCRYPTION AT REST (Server Side)
**Goal:** If server is seized, data is unreadable.
**Estimated Difficulty:** 5/10
**Estimated Time:** 1 Week

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Server Startup: Ask admin to type a password.
- [ ] Use `Argon2` to derive an AES-256 Key from that password.
- [ ] Keep Key in RAM only.
- [ ] When audio arrives:
    - Decrypt Opus (if needed) -> Encrypt AES-256 -> Write to Disk.

---

## ⚪ PHASE 8: REMOTE CONTROL & UPDATES
**Goal:** Update or control the app securely while traveling.
**Estimated Difficulty:** 5/10
**Estimated Time:** 2 Weeks

* **Date Started:** `[ FILL IN DATE ]`
* **Date Finished:** `[ FILL IN DATE ]`
* **Actual Difficulty:** `[ _ / 10 ]`

### Tasks
- [ ] Define Whitelisted Commands (JSON):
    - `{"cmd": "STOP"}`
    - `{"cmd": "START"}`
    - `{"cmd": "CHECK_UPDATE", "url": "..."}`
- [ ] "Safe Mode" Update (Crucial for travel):
    - If you push an update, the app installs it.
    - On first run after update, if it crashes 3 times -> Auto-revert or disable service.
    - *Simpler version:* If it crashes, stop the background service so you can at least open the app and fix it manually.

> **💡 Senior Dev Tip:** Never allow `os.system` or "Execute Arbitrary Code". Only allow specific commands you wrote.

---

## 🏁 FINAL EXAM (Self-Check)

1. [ ] Put phone in Airplane Mode. Talk for 10 mins.
2. [ ] Turn off Airplane Mode.
3. [ ] Do the files appear on the server 1 by 1?
4. [ ] Restart the server. Does the client auto-reconnect?
5. [ ] Restart the phone. Does the app auto-start?

**If YES to all -> Ready for GitHub Release.**