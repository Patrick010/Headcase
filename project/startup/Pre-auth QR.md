pre-auth key as a QR code, ready for a phone or tablet to scan. This eliminates the need to copy anything manually.

1. Update utils.py

Add a function to generate data URIs for QR codes so you can embed them in HTML:

import qrcode
import io
import base64

def generate_qr_data_uri(data: str) -> str:
    """Return a PNG QR code as a base64 data URI"""
    img = qrcode.make(data)
    buffered = io.BytesIO()
    img.save(buffered, format="PNG")
    encoded = base64.b64encode(buffered.getvalue()).decode()
    return f"data:image/png;base64,{encoded}"

2. Update main.py

Change the /admin/approve/{req_id} endpoint to render a confirmation page showing the key + QR code:

from fastapi.responses import HTMLResponse
from utils import generate_preauth_key, generate_qr_data_uri
from fastapi.templating import Jinja2Templates

templates = Jinja2Templates(directory="templates")

@app.post("/admin/approve/{req_id}", response_class=HTMLResponse)
def approve_request(req_id: int, request: Request):
    db = SessionLocal()
    req = db.query(DeviceRequest).filter(DeviceRequest.id == req_id).first()
    if not req:
        return HTMLResponse("Request not found", status_code=404)
    
    # Generate preauth key
    preauth_key = generate_preauth_key()
    req.preauth_key = preauth_key
    req.status = "approved"
    db.commit()
    db.close()
    
    # Generate QR code
    qr_uri = generate_qr_data_uri(preauth_key)
    
    return templates.TemplateResponse("approved.html", {
        "request": request,
        "device_name": req.device_name,
        "preauth_key": preauth_key,
        "qr_uri": qr_uri
    })

3. Create templates/approved.html

<!DOCTYPE html>
<html>
<head>
    <title>Device Approved</title>
</head>
<body>
<h1>Device Approved!</h1>
<p>Device: <strong>{{ device_name }}</strong></p>

<p>PreAuth Key:</p>
<input type="text" value="{{ preauth_key }}" readonly style="width:400px;font-size:1.2em;">

<p>Scan QR Code with Tailscale App:</p>
<img src="{{ qr_uri }}" alt="QR Code">

<p><a href="/admin">Back to Admin Dashboard</a></p>
</body>
</html>

4. Workflow for Admin

    Device submits request via /request.

    Admin goes to /admin, clicks Approve.

    Browser shows approved.html:

        Read-only input with pre-auth key.

        QR code image.

    User scans QR code with Tailscale app on tablet/phone → device joins TailNet automatically.

✅ Benefits

    No clipboard API needed; works on phones, tablets, desktop.

    No manual copying of long keys.

    Immediate visual feedback for admin and end user.

    Compatible with SES-restricted environments.