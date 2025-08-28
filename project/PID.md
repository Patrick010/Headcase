# Project Initiation Document (PID)

**Project Name:** Headcase
**Date:** <DATE>
**Version:** 1.0
**Status:** Live Document

---

## 1. Full Business Case

**Justification:**
Headscale, a self-hosted Tailscale control server, currently requires CLI or pre-auth keys for device registration. This is cumbersome for non-technical users, especially for tablets and mobile devices. This project aims to build a lightweight web interface that simplifies device onboarding.

**Strategic Goals:**
- To eliminate the need for terminal access for device registration.
- To simplify the device onboarding process to a simple "approve device -> join network" flow.
- To provide a cross-platform, mobile-friendly user interface for managing device requests.
- To deliver a secure system with time-limited, single-use pre-auth keys.

**Business Benefits:**
- **Improved Usability:** Simplifies device registration for non-technical users.
- **Increased Efficiency:** Admins can approve devices with a single click.
- **Enhanced Security:** Eliminates the need to share long-lived pre-auth keys manually.
- **Better Accessibility:** Provides a web-based interface that is accessible from any device.

---

## 2. Detailed Project Scope & Product Breakdown

### 2.1 In Scope
- [x] A device request flow where users can submit a request from a web page.
- [x] An admin dashboard to view and manage pending device requests.
- [x] One-click approval/rejection of device requests by an admin.
- [x] Automatic generation of a pre-auth key upon approval.
- [x] Delivery of the pre-auth key to the device as both plain text and a QR code.
- [x] A secure admin panel protected by a login system (to be implemented in Phase 2).

### 2.2 Out of Scope (Current Phase)
- Email or WebSocket notifications for new requests.
- Exporting lists of approved devices for auditing.
- Advanced logging and monitoring for compliance.
- Bulk approval or bulk QR export.

### 2.3 Main Products (Deliverables)
1. **FastAPI Backend:** A Python-based backend handling all API logic, database interactions, and Headscale integration.
2. **Web Interface:** A dynamic web UI with a device request form and an admin dashboard.
3. **Database:** An SQLite database for storing device requests and their status.
4. **Project Documentation:** A full set of project documents including this PID, HLD, LLD, etc., compliant with project standards.

### 2.4 Deferred Features
Deferred features are tracked in `FUTURE_ENHANCEMENTS.md` until they are promoted to an active roadmap phase.

---

## 3. Stage Plans (High-Level)

- **Phase 1 (MVP):** Implement the core device request form, a dynamic admin dashboard, and the inline approve/reject functionality with QR code display.
- **Phase 2 (Security & Polish):** Add admin authentication, enable HTTPS, and enforce pre-auth key expiration.
- **Phase 3 (UX & Advanced Features):** Implement user-facing enhancements such as notifications, bulk approval, and further mobile-friendly UI improvements.

---

## 4. Project Controls

- **Reporting:** Progress tracked in `ACTIVITY.md`, `SESSION_LOG.md` and `CURRENT_STATE.md`. These 3 files, a.k.a. `Trinity` must always be aligned with codereality
- **Change Control:** All changes require proposal, approval, and re-approval if scope deviates.
- **Handling of Postponed Tasks:** Postponed or paused tasks must be moved from the `ACTIVITY.md` log to the `BACKLOG.md` with an appropriate status.
- **Backlog Management and Task Qualification:** The following process is mandatory for managing the `BACKLOG.md`:
  - **Task Generation:** Each task added to the backlog must reference at least one source item from a live project document (e.g., `TRACEABILITY_MATRIX.md`, `USECASES.md`). All tasks must conform to the template defined in `BACKLOG.md`.
  - **Task Qualification:** A task is only eligible for execution if all of its dependencies are resolved and its acceptance criteria are fully defined.
- **Quality Assurance:**
  - Code reviews before merge.
  - Unit/integration testing.
  - Continuous documentation updates in sync with code changes.
  - **Logging of Changes:** All significant changes must be logged and reflected in all relevant project documentation.
  - **Traceability Matrix Maintenance:** `TRACEABILITY_MATRIX.md` is a live document and must be updated with all requirement changes.
  - **Verification of Documentation Integration:** When new documents are created, they must be correctly integrated and referenced in the `PROJECT_REGISTRY.md`.
  - **Structured Logging Mandate:** All new and existing functionality must use the defined logging framework.
  - **Centralized Error Handling Mandate:** All unhandled exceptions must be processed by the Generic Error Handling Module.

---

## 5. Risk, Issue, and Quality Registers

- **Risk Register:**
  - *Risk:* The Headscale CLI or API changes, breaking the integration.
  - *Impact:* The core feature of generating pre-auth keys will fail.
  - *Mitigation:* Abstract the Headscale integration into a dedicated module and have tests covering the expected CLI/API behavior.

- **Issue Register:**
  - *Issue #1:* No admin authentication in the initial MVP.
  - *Status:* Open
  - *Impact:* The admin dashboard is publicly accessible in the MVP.
  - *Action:* Prioritize implementing admin authentication in Phase 2, as per the roadmap.

- **Quality Register:**
  - [All code must be reviewed.]
  - [All docs must be updated with every change.]
  - [Key project state documents (PID, CURRENT_STATE.md, etc.) must remain in sync.]

---

## 6. Project Organisation (Roles & Responsibilities)

- **Project Board / Project Executive:** <STAKEHOLDER_ROLE> — provides mandate, sets requirements, approves plans.
- **Project Manager:** <STAKEHOLDER_ROLE> — manages flow, gives detailed direction.
- **Senior Supplier / Lead Developer:** <TEAM_MEMBER> — responsible for technical design, implementation, testing, and documentation.

---

## 7. Communication Management Approach

- [Describe the primary method of communication, e.g., "All communication via interactive session."]
- [Describe how progress is reported, e.g., "Regular updates and `CURRENT_STATE.md` hand-offs."]
- [Describe how direction is given, e.g., "User provides approvals and new directives."]

---

## 8. Configuration Management Approach

- **Source Code:** Managed in Git with feature branches.
- **Documentation:** Markdown in repo, versioned alongside code.
- **Project State:** Tracked in living docs (`ACTIVITY.md`, `CURRENT_STATE.md`, `PID.md`).

---

## 9. Tailoring Approach

- [Describe how standard project management principles (e.g., PRINCE2, Agile) are adapted for this project's specific workflow.]

---

## Appendix / References

    <link_to_roadmap>
    <link_to_execution_plan>
    <link_to_traceability_matrix>
    <link_to_project_registry>
    <link_to_activity_log>
    <link_to_current_state>
