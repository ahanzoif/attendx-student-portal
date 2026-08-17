

Readme · MD
# AttendX-frontend
 
AttendX is a QR-code based smart attendance system. This repo contains the **frontend** — two single-page HTML apps, one for students and one for teachers — that talk to an AttendX backend API over REST and WebSockets.
 
## Features
 
**Student Portal (`student.html`)**
- Email/password login
- In-browser QR code scanner (via `html5-qrcode`) to mark attendance
- Device fingerprinting (hashed from user agent, screen size, and language) sent with each scan to help flag proxy/duplicate check-ins
- GPS location capture to verify on-campus attendance
- Credibility score display with attendance history
**Teacher Portal (`teacher.html`)**
- Login and live session control (start/end session)
- Auto-generated, rotating QR code for the active session
- Real-time updates via Socket.IO as students check in
- Live "Threat Alerts" feed for suspicious check-ins (e.g. device/location mismatches)
- Countdown timer for session expiry
## Tech Stack
 
- Plain HTML, CSS, and JavaScript (no build step, no framework)
- [html5-qrcode](https://github.com/mebjas/html5-qrcode) for camera-based QR scanning (loaded via CDN)
- [Socket.IO](https://socket.io/) client for real-time teacher dashboard updates
- Browser Geolocation API for GPS capture
## Project Structure
 
```
AttendX-frontend/
├── student.html   # Student login, QR scan, credibility score & history
└── teacher.html   # Teacher login, session control, live dashboard
```
 
## Backend Requirement
 
This is a frontend-only repository. Both pages expect an AttendX backend API running and reachable, and will auto-target either `localhost:3000` or `http://<current-host>:3000` depending on how the page is served:
 
```js
const BACKEND_URL =
  window.location.hostname === 'localhost' ||
  window.location.hostname === '127.0.0.1' ||
  window.location.protocol === 'file:'
    ? 'http://localhost:3000'
    : `http://${window.location.hostname}:3000`;
```
 
Endpoints consumed by these pages include:
 
| Endpoint | Used by | Purpose |
|---|---|---|
| `POST /api/login` | student, teacher | Authenticate user |
| `GET /api/my-score` | student | Fetch credibility score & history |
| `POST /api/scan` | student | Submit a scanned QR code for attendance |
| `POST /api/session/start` | teacher | Start a live attendance session |
| `POST /api/session/end` | teacher | End the current session |
| Socket.IO events | teacher | Live check-in and threat alert updates |
 
Make sure a compatible AttendX backend is running on port `3000` (or update `BACKEND_URL` in each file) before using these pages.
 
## Getting Started
 
1. Clone the repo:
```bash
   git clone https://github.com/vuln-code/AttendX-frontend.git
   cd AttendX-frontend
```
2. Start the AttendX backend separately (not included in this repo) so it's listening on port `3000`.
3. Serve the HTML files with any static server (recommended, since geolocation/camera permissions behave better over `http(s)` than `file://`):
```bash
   npx serve .
   # or
   python3 -m http.server 8080
```
4. Open `student.html` for the student view or `teacher.html` for the teacher/live dashboard view.
## Notes
 
- Camera access (for QR scanning) and geolocation both require the page to be served over `localhost` or HTTPS — most browsers block these on plain `http://` for non-localhost origins.
- The device fingerprint is a simple client-side hash, not a security-grade identifier — the backend should treat it as one signal among several rather than a trust boundary.
## License
 
No license specified yet.
 
