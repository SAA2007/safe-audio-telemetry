# Project S.A.F.E. Development Roadmap

**Project Goal:** Build a fault-tolerant, store-and-forward encrypted audio telemetry system for personal devices, designed to operate reliably in intermittent network conditions.

**Tech Stack:** * **Client:** Android (Java/Kotlin), Foreground Services, Opus Codec
* **Server:** Python (FastAPI), Async WebSockets, Argon2/AES-256
* **Protocol:** Custom Store-and-Forward over TCP

---

## 🟢 Phase 0: Foundation & Ethics
- [ ] Initialize Repository structure (`android-client`, `python-server`).
- [ ] Define Threat Model: System protects against network interception (MITM) and unauthorized server access.
- [ ] Establish Ethical Constraints: Application requires explicit user consent and visible foreground notification (Android Service) to operate.

## 🟡 Phase 1: Server Infrastructure
- [ ] Implement Python FastAPI backend.
- [ ] Establish Async WebSocket endpoints for real-time signaling.
- [ ] Implement basic handshake protocol (`HELO` / `WELCOME`).
- [ ] **Technical Goal:** Ensure stable TCP connections before introducing audio data.

## 🟠 Phase 2: Android Core Services
- [ ] Implement Android Foreground Service with persistent low-priority notification.
- [ ] Integrate `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` to prevent OS-level process killing (Doze mode mitigation).
- [ ] Implement `BootReceiver` for automatic service restoration after device reboot.
- [ ] Establish WebSocket bridge to Python server using OkHttp.

## 🟠 Phase 3: Audio Acquisition
- [ ] Implement `AudioRecord` API (16kHz, Mono, 16-bit PCM).
- [ ] Create user consent UI for microphone permissions.
- [ ] Implement local buffering to raw `.wav` files for verification.

## 🔴 Phase 4: Data Compression
- [ ] Integrate Opus Audio Codec via JNI/Native libraries.
- [ ] Implement PCM-to-Opus encoding pipeline.
- [ ] **Target Metric:** Achieve <10kbps bitrate (approx. 5MB/hour) while maintaining voice intelligibility.

## 🔴 Phase 5: Store-and-Forward Engine (Core Architecture)
- [ ] **Chunking Logic:** Segment continuous audio into timestamped, immutable chunks.
- [ ] **Queue System:** Implement local FIFO queue (`/pending_uploads`) for offline persistence.
- [ ] **Worker Thread:** Background logic to monitor network state.
    - If Online: Upload chunk -> Await `ACK` -> Delete local file.
    - If Offline: Retain chunk in queue (Zero Data Loss guarantee).

## 🔴 Phase 6: Identity & Authentication
- [ ] Implement Public Key Infrastructure (PKI) for device identity.
- [ ] **Client:** Generate Hardware-backed Keypair.
- [ ] **Server:** Enforce whitelist-based authentication (`allowed_clients.json`).
- [ ] Challenge-Response mechanism to prevent unauthorized connections.

## 🟡 Phase 7: Data Security (Encryption at Rest)
- [ ] Implement Server-side encryption pipeline.
- [ ] **Key Derivation:** Argon2 hashing of admin password to derive AES-256 keys.
- [ ] **Storage:** Data written to disk is encrypted immediately; keys reside only in RAM.

## 🟡 Phase 8: Remote Management
- [ ] Define JSON Command Protocol (e.g., `STOP`, `START`, `CHECK_UPDATE`).
- [ ] Implement strict command whitelisting (No arbitrary code execution).
- [ ] **Resilience:** "Safe Mode" update logic to prevent crash loops on remote devices.

---

### ✅ Success Criteria
1.  **Resilience:** Zero data loss during network transitions (WiFi <-> LTE <-> Offline).
2.  **Efficiency:** Battery drain <5% per hour; Data usage <100MB/month.
3.  **Security:** All data encrypted in transit (TLS) and at rest (AES-256).