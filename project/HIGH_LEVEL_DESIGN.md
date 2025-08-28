# High-Level Design (HLD) – Headcase

**Status:** Live Document

## 1. Purpose
This document outlines the high-level architecture, scope, and guiding principles for the Headcase project. It serves as a blueprint for the development team to create a simple, secure, and user-friendly web UI for Headscale device onboarding.

## 2. Scope
The project aims to:
- Build a lightweight web interface for device registration and approval.
- Integrate with a Headscale server to generate pre-auth keys.
- Provide a dynamic, single-page-like experience for administrators.
- Ensure the solution is secure and easy to deploy.

## 3. Architecture Overview
The Headcase application follows a simple, monolithic backend-for-frontend (BFF) architecture. It is composed of a single FastAPI application that serves both a REST API and the frontend web pages.

**Key Layers & Components:**
1.  **Web Interface (Frontend):**
    *   Standard HTML5, CSS, and vanilla JavaScript served directly by FastAPI.
    *   Uses Jinja2 for templating.
    *   Consists of two main pages: a device request form (`/request`) and an admin dashboard (`/admin`).
    *   The admin dashboard is dynamic, using `fetch` to call the backend API and update the UI without page reloads.

2.  **API Layer (Backend):**
    *   A set of RESTful endpoints built with FastAPI.
    *   Handles requests from the web interface for submitting device requests and managing them.
    *   Returns JSON responses for dynamic UI updates.

3.  **Persistence Layer:**
    *   An SQLite database to store the state of device requests (pending, approved, rejected).
    *   Uses SQLAlchemy for object-relational mapping (ORM).
    *   The database schema is defined in `models.py`.

4.  **Headscale Integration Layer:**
    *   A utility module (`utils.py`) that communicates with the Headscale server.
    *   Uses Python's `subprocess` module to execute the `headscale` command-line tool for generating pre-auth keys.
    *   This layer is responsible for creating and returning the keys needed for device onboarding.

**Data Flow Example (Admin Approval):**
1.  Admin clicks the "Approve" button on the admin dashboard.
2.  JavaScript in the browser sends a `POST` request to the `/admin/approve/{id}` API endpoint.
3.  The FastAPI route handler receives the request.
4.  The handler calls the `generate_preauth_key()` function in the Headscale Integration Layer.
5.  This function executes `headscale preauthkeys create ...` and captures the output.
6.  The handler updates the device request's status and stores the new pre-auth key in the SQLite database via the Persistence Layer.
7.  The API endpoint returns a JSON response containing the pre-auth key and a base64-encoded QR code.
8.  The frontend JavaScript receives the JSON and dynamically updates the table row to display the key and QR code.

## 4. Non-Functional Requirements
- **Performance**: The application should be lightweight with fast response times, suitable for running on low-power hardware.
- **Security**: The admin dashboard must be protected by an authentication mechanism (Phase 2). All pre-auth keys should be time-limited.
- **Usability**: The interface should be simple and intuitive for non-technical users. It must be responsive and work well on mobile devices.

## 5. Deployment Model
- **Development**: Run locally using `uvicorn` with an SQLite database file.
- **Production**: Recommended to run as a containerized application (e.g., Docker). It can be placed behind a reverse proxy like Caddy to handle HTTPS.

## 6. Security Model
- **Admin Access**: To be secured with a username/password login system (Phase 2).
- **Pre-auth Keys**: Generated with a limited expiration time (e.g., 24 hours) and are ideally single-use to minimize risk.
- **Dependencies**: Keep all Python packages updated to patch potential vulnerabilities.

## 7. Risks & Mitigations
- **Risk**: The `headscale` CLI tool's output format changes, breaking the integration.
  **Mitigation**: The integration logic is isolated in `utils.py`. Tests should be written to validate the expected output of the CLI command, which would fail if the format changes, alerting developers to the issue.
- **Risk**: The admin interface is exposed without authentication in the MVP.
  **Mitigation**: This is a known and accepted risk for the MVP. The `ROADMAP.md` explicitly prioritizes adding authentication in Phase 2, before any production deployment.
