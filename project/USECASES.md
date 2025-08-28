# Headcase – User-Driven Use Cases

This document captures the primary user scenarios that the Headcase application is designed to support.

---

## 1. New Device Onboarding Request

**Actor:** A user with a new device (e.g., a tablet, phone, or laptop).

**Scenario:**
The user wants to add their new device to the company's Headscale network. They do not have terminal access or technical knowledge of Headscale.

1.  The user navigates to the Headcase web portal's request page (e.g., `http://headcase.example.com/request`).
2.  They are presented with a simple form.
3.  They enter their device's name (e.g., "John's iPad") and their own name into the form fields.
4.  They click the "Submit Request" button.
5.  They see a confirmation message that their request has been submitted for approval.

**Requirements:**
- A public-facing web page with a simple form.
- The ability to submit the device name and user name to the backend.
- The backend must create a new record for the request with a "pending" status.

---

## 2. Administrator Approves a Device

**Actor:** A network administrator.

**Scenario:**
The administrator needs to approve a new device request that has been submitted by a user. They want to do this quickly and securely from a central dashboard.

1.  The administrator navigates to the Headcase admin dashboard (e.g., `http://headcase.example.com/admin`).
2.  They see a table of pending device requests, including "John's iPad".
3.  The administrator clicks the "Approve" button next to the request.
4.  The UI updates instantly and in-line, without a page reload. The "Approve" and "Reject" buttons are replaced with:
    *   A read-only text field containing a new, single-use pre-auth key.
    *   A QR code that contains the same pre-auth key.
5.  The administrator contacts the user and instructs them to scan the QR code with their device's Tailscale app.
6.  The user's device successfully joins the network.

**Requirements:**
- A protected web page for administrators.
- A table listing all pending device requests.
- "Approve" and "Reject" buttons for each request.
- The "Approve" action must trigger the backend to:
    - Generate a time-limited, single-use pre-auth key via the Headscale CLI.
    - Update the request's status to "approved" in the database.
    - Return the key and a QR code representation to the frontend.
- The frontend must dynamically update the UI to display the key and QR code.
- The "Reject" action should remove the request from the pending list.
