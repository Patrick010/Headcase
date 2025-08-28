Proposal: Headscale Web UI for Simple Device Onboarding
Project Overview

Headscale, a self-hosted Tailscale control server, currently requires CLI or pre-auth keys for device registration. This is cumbersome for non-technical users, especially for tablets and mobile devices.

The goal is to build a lightweight web interface that allows:

Devices to request access.

Admins to approve requests with one click.

Devices to automatically receive pre-auth keys or QR codes to join the TailNet.

Cross-platform usability, including mobile-friendly interfaces.

The UI will eliminate the need for terminal access, simplify device onboarding, and mimic Tailscale Cloud’s “approve device → join network” flow.

Functional Requirements
1. Device Request Flow

Devices access a web page (/request) and submit:

Device name

Optional user or owner info

Backend logs request in a database (SQLite/Postgres).

Request appears in admin dashboard for approval.

2. Admin Dashboard

Accessible only to authenticated admins.

Displays pending device requests in a table.

Each request shows:

Device name

User info

Request timestamp

Status (Pending / Approved / Rejected)

Admin can approve or reject a request with one click.

On approval:

Backend generates a pre-auth key via Headscale CLI or API.

Backend marks request as approved.

Key is delivered to device (plain text and QR code).

3. Device Onboarding

After approval, device sees:

The pre-auth key in a read-only input field.

A QR code that can be scanned by Tailscale app.

Optional: auto-copy button (fallbacks if clipboard API is blocked).

4. Security

Admin panel protected via login system (JWT or session cookies).

All pre-auth keys are time-limited and optionally single-use.

Database stores minimal sensitive info (keys expire automatically).

Optional HTTPS for secure access (Caddy or Python HTTPS server).

5. Additional Features

Notifications: email or WebSocket alerts for new requests.

Export list of approved devices for auditing.

Mobile-friendly layout (responsive design).

Logging and monitoring for compliance.

Technical Stack
Backend

Language: Python 3.11+

Framework: FastAPI (for REST endpoints)

Database: SQLite (for lightweight deployments) or Postgres (for production)

Headscale Integration:

Use subprocess to call headscale preauthkeys create

Or, if Headscale exposes an API, integrate directly.

Frontend

Minimal HTML/CSS/JS served from FastAPI (or static files).

Optional: use SvelteKit or Vue for richer interactivity.

Responsive design to support phones/tablets.

QR code generation via qrcode Python library.

Endpoints
Endpoint	Method	Description
/api/request	POST	Submit a device registration request
/api/pending	GET	Admin fetches pending requests
/api/approve/{id}	POST	Admin approves a device request and generates pre-auth key
/api/reject/{id}	POST	Admin rejects a device request
/api/key/{id}	GET	Device fetches approved pre-auth key (or QR code)