# Low-Level Design (LLD) – Headcase

## Purpose
This LLD describes the specific implementation details of the Headcase application, including its file structure, database models, API endpoints, and frontend logic.

---

## Project Structure
The application is contained within a single directory, `headcase_webui/`, with the following structure:

```
headcase_webui/
├── main.py          # FastAPI application entrypoint, contains all routes
├── models.py        # SQLAlchemy database models
├── utils.py         # Helper functions for Headscale integration and QR codes
├── templates/
│   ├── request.html # Jinja2 template for the device request form
│   └── admin.html   # Jinja2 template for the admin dashboard
├── requirements.txt # Python package dependencies
└── database.db      # SQLite database file (auto-created)
```

---

## Database Design

**Module:** `models.py`

The application uses SQLAlchemy as its ORM. A single table, `device_requests`, is used to track all device registration requests.

**`DeviceRequest` Model:**
```python
from sqlalchemy import Column, Integer, String, DateTime
from sqlalchemy.ext.declarative import declarative_base
import datetime

Base = declarative_base()

class DeviceRequest(Base):
    __tablename__ = "device_requests"
    id = Column(Integer, primary_key=True, index=True)
    device_name = Column(String, nullable=False)
    user_name = Column(String, nullable=True)
    status = Column(String, default="pending")  # Can be: pending, approved, rejected
    preauth_key = Column(String, nullable=True)
    timestamp = Column(DateTime, default=datetime.datetime.utcnow)
    expiration = Column(DateTime, nullable=True) # For the pre-auth key
```

---

## Backend Implementation

**Module:** `main.py`

The backend is a single FastAPI application.

*   **Database Connection:** A SQLAlchemy engine and session are created to connect to the `sqlite:///./database.db` file.
*   **Templates:** A `Jinja2Templates` instance is configured to serve HTML templates from the `templates/` directory.

### API Endpoints
The following endpoints are defined:

1.  **`GET /request`**
    *   **Response:** `HTMLResponse`
    *   **Description:** Renders and returns the `request.html` template.

2.  **`POST /request`**
    *   **Request Body:** Form data with `device_name` (str) and optional `user_name` (str).
    *   **Response:** `{"status": "submitted"}`
    *   **Description:** Creates a new `DeviceRequest` record in the database with a "pending" status.

3.  **`GET /admin`**
    *   **Response:** `HTMLResponse`
    *   **Description:** Fetches all device requests with a "pending" status from the database and renders the `admin.html` template with this data.

4.  **`POST /admin/approve/{req_id}`**
    *   **Path Parameter:** `req_id` (int)
    *   **Response:** JSON object `{"id": int, "device_name": str, "preauth_key": str, "qr_uri": str}`
    *   **Description:**
        1.  Finds the `DeviceRequest` by its ID.
        2.  Calls `utils.generate_preauth_key()` to get a new key from Headscale.
        3.  Updates the request's status to "approved" and saves the key.
        4.  Calls `utils.generate_qr_data_uri()` to create a base64 QR code image.
        5.  Returns the key details in a JSON response.

5.  **`POST /admin/reject/{req_id}`**
    *   **Path Parameter:** `req_id` (int)
    *   **Response:** `{"status": "rejected"}`
    *   **Description:** Finds the `DeviceRequest` by its ID and updates its status to "rejected".

---

## Utility Functions

**Module:** `utils.py`

This module contains helper functions decoupled from the main application logic.

*   **`generate_preauth_key() -> str`**
    *   Executes `headscale preauthkeys create --reusable --expiration 24h --json` using `subprocess.run()`.
    *   Parses the JSON output from the command to extract and return the pre-auth key.

*   **`generate_qr_data_uri(data: str) -> str`**
    *   Takes a string (the pre-auth key) as input.
    *   Uses the `qrcode` library to generate a PNG image.
    *   Encodes the image into a base64 data URI string (`data:image/png;base64,...`) suitable for embedding directly in HTML.

---

## Frontend Implementation

**Module:** `templates/admin.html`

The admin dashboard's dynamic behavior is implemented with vanilla JavaScript inside a `<script>` tag.

*   **Event Listeners:** Attaches `click` event listeners to all buttons with the class `approve-btn` and `reject-btn`.
*   **Approve Flow:**
    1.  When an "Approve" button is clicked, it gets the request ID from the parent table row's `data-id` attribute.
    2.  It sends a `POST` request to `/admin/approve/{id}` using `fetch`.
    3.  On receiving a successful JSON response, it dynamically creates an `<input>` field and an `<img>` tag.
    4.  It populates these new elements with the `preauth_key` and `qr_uri` from the response.
    5.  It injects this HTML into the table cell with the class `key-cell`, replacing the action buttons.
*   **Reject Flow:**
    1.  When a "Reject" button is clicked, it gets the request ID.
    2.  It sends a `POST` request to `/admin/reject/{id}`.
    3.  Upon completion, it removes the entire table row (`<tr>`) from the DOM.
