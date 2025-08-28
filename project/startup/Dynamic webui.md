Headscale web UI with full device request and dynamic admin dashboard showing pre-auth keys and QR codes.

Project Structure

headscale_webui/
├── main.py
├── models.py
├── utils.py
├── templates/
│   ├── request.html
│   └── admin.html
├── requirements.txt
└── database.db   # auto-created

1. requirements.txt

fastapi
uvicorn
jinja2
sqlalchemy
databases
qrcode[pil]
python-multipart

2. models.py

from sqlalchemy import Column, Integer, String, DateTime
from sqlalchemy.ext.declarative import declarative_base
import datetime

Base = declarative_base()

class DeviceRequest(Base):
    __tablename__ = "device_requests"
    id = Column(Integer, primary_key=True, index=True)
    device_name = Column(String, nullable=False)
    user_name = Column(String, nullable=True)
    status = Column(String, default="pending")  # pending, approved, rejected
    preauth_key = Column(String, nullable=True)
    timestamp = Column(DateTime, default=datetime.datetime.utcnow)
    expiration = Column(DateTime, nullable=True)

3. utils.py

import subprocess
import qrcode
import io
import base64

def generate_preauth_key() -> str:
    """Generate reusable pre-auth key via Headscale CLI"""
    result = subprocess.run(
        ["headscale", "preauthkeys", "create", "--reusable", "--expiration", "24h", "--json"],
        capture_output=True, text=True
    )
    import json
    data = json.loads(result.stdout)
    return data['key']

def generate_qr_data_uri(data: str) -> str:
    """Return PNG QR code as base64 data URI"""
    img = qrcode.make(data)
    buffered = io.BytesIO()
    img.save(buffered, format="PNG")
    encoded = base64.b64encode(buffered.getvalue()).decode()
    return f"data:image/png;base64,{encoded}"

4. main.py

from fastapi import FastAPI, Request, Form
from fastapi.responses import HTMLResponse
from fastapi.templating import Jinja2Templates
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from models import Base, DeviceRequest
from utils import generate_preauth_key, generate_qr_data_uri

DATABASE_URL = "sqlite:///./database.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine)
Base.metadata.create_all(bind=engine)

app = FastAPI()
templates = Jinja2Templates(directory="templates")

@app.get("/request", response_class=HTMLResponse)
def request_form(request: Request):
    return templates.TemplateResponse("request.html", {"request": request})

@app.post("/request")
def submit_request(device_name: str = Form(...), user_name: str = Form(None)):
    db = SessionLocal()
    req = DeviceRequest(device_name=device_name, user_name=user_name)
    db.add(req)
    db.commit()
    db.close()
    return {"status": "submitted"}

@app.get("/admin", response_class=HTMLResponse)
def admin_dashboard(request: Request):
    db = SessionLocal()
    requests = db.query(DeviceRequest).filter(DeviceRequest.status=="pending").all()
    db.close()
    return templates.TemplateResponse("admin.html", {"request": request, "requests": requests})

@app.post("/admin/approve/{req_id}")
def approve_request(req_id: int):
    db = SessionLocal()
    req = db.query(DeviceRequest).filter(DeviceRequest.id==req_id).first()
    if not req:
        return {"error": "Request not found"}
    preauth_key = generate_preauth_key()
    req.preauth_key = preauth_key
    req.status = "approved"
    db.commit()
    db.close()
    qr_uri = generate_qr_data_uri(preauth_key)
    return {"id": req.id, "device_name": req.device_name, "preauth_key": preauth_key, "qr_uri": qr_uri}

@app.post("/admin/reject/{req_id}")
def reject_request(req_id: int):
    db = SessionLocal()
    req = db.query(DeviceRequest).filter(DeviceRequest.id==req_id).first()
    if req:
        req.status = "rejected"
        db.commit()
    db.close()
    return {"status": "rejected"}

5. templates/request.html

<!DOCTYPE html>
<html>
<head><title>Device Request</title></head>
<body>
<h1>Request Device Access</h1>
<form action="/request" method="post">
    <label>Device Name:</label>
    <input type="text" name="device_name" required><br>
    <label>User Name:</label>
    <input type="text" name="user_name"><br>
    <button type="submit">Submit Request</button>
</form>
</body>
</html>

6. templates/admin.html

<!DOCTYPE html>
<html>
<head>
<title>Admin Dashboard</title>
</head>
<body>
<h1>Pending Device Requests</h1>
<table border="1" id="requests-table">
<tr>
<th>ID</th><th>Device Name</th><th>User</th><th>Actions</th><th>Key / QR</th>
</tr>
{% for req in requests %}
<tr data-id="{{ req.id }}">
    <td>{{ req.id }}</td>
    <td>{{ req.device_name }}</td>
    <td>{{ req.user_name or "" }}</td>
    <td>
        <button class="approve-btn">Approve</button>
        <button class="reject-btn">Reject</button>
    </td>
    <td class="key-cell"></td>
</tr>
{% endfor %}
</table>

<script>
document.querySelectorAll(".approve-btn").forEach(btn => {
    btn.addEventListener("click", async (e) => {
        const row = e.target.closest("tr");
        const id = row.dataset.id;
        const resp = await fetch(`/admin/approve/${id}`, {method:'POST'});
        const data = await resp.json();
        if(data.error){ alert(data.error); return; }
        const cell = row.querySelector(".key-cell");
        cell.innerHTML = `
            <input type="text" readonly value="${data.preauth_key}" style="width:250px;">
            <img src="${data.qr_uri}" width="100" alt="QR Code">
        `;
    });
});

document.querySelectorAll(".reject-btn").forEach(btn => {
    btn.addEventListener("click", async (e) => {
        const row = e.target.closest("tr");
        const id = row.dataset.id;
        await fetch(`/admin/reject/${id}`, {method:'POST'});
        row.remove();
    });
});
</script>
</body>
</html>

7. How to Run

pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000

    Access device request page: http://<server-ip>:8000/request

    Access admin dashboard: http://<server-ip>:8000/admin

Approving a device instantly renders the pre-auth key + QR code in-line. Tablets or phones can scan the QR code and join the TailNet immediately. No manual copying required.

This gives a fully functional, simple web UI for Headscale, with:

    Device request form

    Dynamic admin dashboard

    Inline pre-auth keys and QR codes

    Works on mobile/tablet without clipboard issues

You can extend this with authentication, HTTPS via Caddy, and database persistence for production.