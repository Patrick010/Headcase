admin dashboard fully dynamic, so you can approve multiple devices in-line and instantly see their pre-auth keys and QR codes without leaving the page.

1. Switch to JSON endpoints

Instead of returning a new HTML page for each approval, make /admin/approve/{req_id} return JSON:

@app.post("/admin/approve/{req_id}")
def approve_request_json(req_id: int):
    db = SessionLocal()
    req = db.query(DeviceRequest).filter(DeviceRequest.id == req_id).first()
    if not req:
        return {"error": "Request not found"}
    
    preauth_key = generate_preauth_key()
    req.preauth_key = preauth_key
    req.status = "approved"
    db.commit()
    db.close()
    
    qr_uri = generate_qr_data_uri(preauth_key)
    
    return {
        "id": req.id,
        "device_name": req.device_name,
        "preauth_key": preauth_key,
        "qr_uri": qr_uri
    }

2. Update admin.html with JavaScript

Use JS to fetch the JSON endpoint and update the table dynamically:

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
        const resp = await fetch(`/admin/approve/${id}`, {method: 'POST'});
        const data = await resp.json();
        if (data.error) {
            alert(data.error);
            return;
        }
        const keyCell = row.querySelector(".key-cell");
        keyCell.innerHTML = `
            <input type="text" readonly value="${data.preauth_key}" style="width:250px;">
            <img src="${data.qr_uri}" alt="QR Code" width="100">
        `;
    });
});

document.querySelectorAll(".reject-btn").forEach(btn => {
    btn.addEventListener("click", async (e) => {
        const row = e.target.closest("tr");
        const id = row.dataset.id;
        await fetch(`/admin/reject/${id}`, {method: 'POST'});
        row.remove();
    });
});
</script>

3. How it works

    Page loads with pending requests in a table.

    Admin clicks Approve → JS calls /admin/approve/{id}.

    JSON response contains:

        preauth_key

        qr_uri (base64 QR code)

    JS updates the same row with:

        Read-only key input

        QR code image

    No page reload needed, fully in-line.

Reject buttons remove rows dynamically as well.
✅ Benefits

    One-screen workflow for multiple devices.

    Immediate visual feedback.

    Works on phones/tablets without worrying about clipboard APIs.

    QR codes ready to scan immediately.

    Lightweight and easy to extend.