# Project API Endpoints Reference

## Overview

This file lists all public API endpoints for the Headcase project. It provides a high-level reference for developers.

Note: Authentication is planned for Phase 2. In the current MVP, no endpoints require authentication.

---

## Headcase API Endpoints

### Device Requests
| Method | Path | Summary | Auth Required |
|---|---|---|---|
| GET | `/request` | Returns the HTML page for the device request form. | No |
| POST | `/request` | Submits a new device registration request. | No |

### Admin Dashboard
| Method | Path | Summary | Auth Required |
|---|---|---|---|
| GET | `/admin` | Returns the HTML page for the admin dashboard. | No (Planned) |
| POST | `/admin/approve/{req_id}` | Approves a device request and returns a pre-auth key. | No (Planned) |
| POST | `/admin/reject/{req_id}` | Rejects a device request. | No (Planned) |
