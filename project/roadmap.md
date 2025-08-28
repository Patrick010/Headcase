Here’s a step-by-step plan to stitch everything together into a fully functional, user-friendly Headscale web UI that works on phones, tablets, and desktops. This covers backend, frontend, deployment, and integration with Headscale.

1. Define Components and Responsibilities
Component	Responsibility
FastAPI backend	Handle device requests, admin approvals, generate pre-auth keys, return QR codes, serve HTML templates.
Database (SQLite/Postgres)	Track device requests, pre-auth key status, timestamps, expiration.
Templates (Jinja2)	Render /request device form and /admin dashboard dynamically.
Static assets	CSS/JS for dynamic updates, responsive design, QR display.
Headscale CLI/API	Generate pre-auth keys (headscale preauthkeys create) and integrate with existing Headscale instance.
Optional Caddy proxy	Provide HTTPS, reverse proxy for production.

2. Setup Backend

Create project folder (headscale_webui/).

Install dependencies: FastAPI, SQLAlchemy, Jinja2, QR code library, Uvicorn.

Implement models.py with DeviceRequest table.

Implement utils.py for pre-auth key generation and QR code creation.

Implement main.py with routes:

/request (GET + POST)

/admin (GET)

/admin/approve/{req_id} (POST, returns JSON with key + QR)

/admin/reject/{req_id} (POST)

3. Build Frontend Templates

Device request page (request.html)

Form to submit device name + optional user.

Submit button calls POST /request.

Admin dashboard (admin.html)

Table of pending requests.

Approve/reject buttons per row.

Inline column for pre-auth key + QR code, updated dynamically via JS.

JS fetches JSON endpoints for approve/reject actions.

4. Implement Dynamic Behavior

Approve button triggers JS fetch to /admin/approve/{id}.

On success, update table row with:

Read-only input with pre-auth key

Embedded QR code (data:image/png;base64,...)

Reject button removes row immediately via JS.

No page reloads required.

Ensure mobile-friendly layout with simple CSS.

5. Security and Usability

Admin authentication (session or JWT).

Pre-auth keys:

Time-limited (e.g., 24h)

Optional single-use enforcement

Serve backend over HTTPS (via Caddy reverse proxy).

Optional CSRF protection for forms.

6. Headscale Integration

Backend calls headscale preauthkeys create --reusable --expiration 24h --json.

Store generated key in DB with request ID.

QR code generated from the key.

Devices can scan QR code to join TailNet immediately.

7. Deployment Plan

Local development

Run uvicorn main:app --reload.

Access device page and admin dashboard via HTTP.

Production

Serve via Caddy: HTTPS + reverse proxy to FastAPI backend.

Run FastAPI behind systemd or Docker (optional).

Ensure Headscale is accessible from backend.

Optional enhancements

Notifications for new requests (email/WebSocket).

Bulk approval or bulk QR export.

Logging/audit trail for approvals.

8. Flow Recap

Device opens /request → submits name.

Request saved in DB → appears on admin dashboard.

Admin clicks Approve → backend generates key + QR.

JS updates table row → user can scan QR code or copy key.

Device joins TailNet immediately.

Optional expiration/single-use ensures security.

9. Next Steps / Roadmap

Phase 1 (MVP):

Device request form

Admin dashboard

Inline approve/reject with QR display

Phase 2 (Security & polish):

Admin authentication

HTTPS

Key expiration enforcement

Phase 3 (UX & advanced features):

Notifications

Bulk approval

Mobile-friendly UI enhancements

This plan stitches backend, frontend, and Headscale CLI together into a seamless workflow: devices request, admin approves, device gets a QR code to join — all without needing terminals or clipboard APIs.

You can follow this roadmap to implement the full system, then incrementally add security, notifications, and advanced UX features.