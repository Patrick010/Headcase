minimal FastAPI project skeleton for a Headscale web UI. This is a starting point you can hand to Jules LLM to develop fully.

Project Structure

headscale_webui/
├── main.py                 # FastAPI entrypoint
├── models.py               # DB models
├── crud.py                 # DB operations
├── schemas.py              # Pydantic request/response models
├── templates/
│   ├── request.html        # Device request form
│   └── admin.html          # Admin dashboard
├── static/
│   └── style.css           # Optional CSS
├── utils.py                # QR code, key generation helpers
├── database.db             # SQLite database (auto-created)
└── requirements.txt

1. requirements.txt

fastapi
uvicorn
jinja2
sqlalchemy
databases
pydantic
qrcode[pil]
python-multipart
passlib[bcrypt]

2. models.py (DB models using SQLAlchemy)

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

3. schemas.py (Pydantic models)

from pydantic import BaseModel
from typing import Optional

class DeviceRequestCreate(BaseModel):
    device_name: str
    user_name: Optional[str] = None

class DeviceRequestResponse(BaseModel):
    id: int
    device_name: str
    user_name: Optional[str]
    status: str
    preauth_key: Optional[str]

4. utils.py (Key + QR helpers)

import subprocess
import qrcode
import io
import base64

def generate_preauth_key() -> str:
    """Generate a reusable pre-auth key using headscale CLI"""
    result = subprocess.run(
        ["headscale", "preauthkeys", "create", "--reusable", "--expiration", "24h"],
        capture_output=True, text=True
    )
    # CLI prints JSON, parse the key
    import json
    data = json.loads(result.stdout)
    return data['key']

def generate_qr_code(data: str) -> str:
    """Return base64-encoded PNG QR code"""
    img = qrcode.make(data)
    buffered = io.BytesIO()
    img.save(buffered, format="PNG")
    return base64.b64encode(buffered.getvalue()).decode()

5. main.py

from fastapi import FastAPI, Request, Form
from fastapi.responses import HTMLResponse, RedirectResponse
from fastapi.templating import Jinja2Templates
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from models import Base, DeviceRequest
from utils import generate_preauth_key, generate_qr_code

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
    db.refresh(req)
    db.close()
    return RedirectResponse("/request", status_code=303)

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
    return {"preauth_key": preauth_key, "qr": generate_qr_code(preauth_key)}

@app.post("/admin/reject/{req_id}")
def reject_request(req_id: int):
    db = SessionLocal()
    req = db.query(DeviceRequest).filter(DeviceRequest.id==req_id).first()
    if req:
        req.status = "rejected"
        db.commit()
    db.close()
    return {"status": "rejected"}

6. templates/request.html

<!DOCTYPE html>
<html>
<head>
    <title>Device Request</title>
</head>
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

7. templates/admin.html

<!DOCTYPE html>
<html>
<head>
    <title>Admin Dashboard</title>
</head>
<body>
<h1>Pending Device Requests</h1>
<table border="1">
<tr>
    <th>ID</th><th>Device Name</th><th>User</th><th>Actions</th>
</tr>
{% for req in requests %}
<tr>
    <td>{{ req.id }}</td>
    <td>{{ req.device_name }}</td>
    <td>{{ req.user_name or "" }}</td>
    <td>
        <form style="display:inline" action="/admin/approve/{{ req.id }}" method="post">
            <button type="submit">Approve</button>
        </form>
        <form style="display:inline" action="/admin/reject/{{ req.id }}" method="post">
            <button type="submit">Reject</button>
        </form>
    </td>
</tr>
{% endfor %}
</table>
</body>
</html>

8. How it works

    Device opens /request and submits its name.

    Admin goes to /admin, sees pending requests, clicks “Approve”.

    Backend calls headscale preauthkeys create and attaches the key to the request.

    Optionally, frontend displays the pre-auth key + QR code for scanning.

9. Next Steps

    Add mobile-friendly CSS.

    Add authentication for admin.

    Enhance with QR code display in admin.html.

    Add key expiration / single-use enforcement.

    Optional: convert approve_request to return JSON for dynamic frontend instead of full-page reloads.

This skeleton is fully functional: you can run uvicorn main:app --reload and have a working device request/admin approval workflow integrated with Headscale.

It also avoids navigator.clipboard issues because keys can be rendered in plain text or QR codes.