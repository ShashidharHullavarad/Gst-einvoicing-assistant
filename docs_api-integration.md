# API Integration Guide — NIC IRP & e-Way Bill APIs

This guide covers integrating live IRN and e-Way Bill status lookups into the GST Assistant.

---

## 1. NIC IRP API (e-Invoice)

### Base URL
```
https://api.einvoice1.gst.gov.in
```

### Authentication (Step-by-step)

#### Step 1 — Generate OTP
```http
POST /auth/generateotp
Content-Type: application/json

{
  "gstin": "29AABCT1332L1ZD",
  "username": "your_nic_username"
}
```
OTP is sent to the registered mobile number.

#### Step 2 — Verify OTP and Get Token
```http
POST /auth/verifyotp
Content-Type: application/json

{
  "gstin": "29AABCT1332L1ZD",
  "username": "your_nic_username",
  "otp": "123456"
}
```
Response includes `auth_token` (valid 6 hours).

#### Step 3 — Get IRN Details
```http
GET /irnstatus/v2/{irn}
Authorization: Bearer {auth_token}
```

### Python Example
```python
import requests
import os

IRP_BASE = "https://api.einvoice1.gst.gov.in"
GSTIN    = os.environ["IRP_GSTIN"]
USERNAME = os.environ["IRP_USERNAME"]

def get_irp_token(otp: str) -> str:
    resp = requests.post(f"{IRP_BASE}/auth/verifyotp", json={
        "gstin": GSTIN, "username": USERNAME, "otp": otp
    })
    return resp.json()["data"]["auth_token"]

def get_irn_status(irn: str, token: str) -> dict:
    resp = requests.get(
        f"{IRP_BASE}/irnstatus/v2/{irn}",
        headers={"Authorization": f"Bearer {token}"}
    )
    return resp.json()
```

---

## 2. NIC e-Way Bill API

### Base URL
```
https://api.ewaybillgst.gov.in
```

### Key Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/ewayapi/getewaybill` | GET | Get full EWB details |
| `/ewayapi/getewbstatus` | GET | Get EWB status only |
| `/ewayapi/rejectewb` | POST | Reject an EWB |
| `/ewayapi/extendvalidity` | POST | Extend EWB validity |

### Get EWB Status
```http
GET /ewayapi/getewbstatus?ewbno=121000000003
Authorization: Bearer {auth_token}
```

### Python Example
```python
EWB_BASE = "https://api.ewaybillgst.gov.in"

def get_ewb_status(ewb_number: str, token: str) -> dict:
    resp = requests.get(
        f"{EWB_BASE}/ewayapi/getewbstatus",
        headers={"Authorization": f"Bearer {token}"},
        params={"ewbno": ewb_number}
    )
    return resp.json()
```

---

## 3. FastAPI Integration Example

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI(title="GST Assistant Backend")

class IRNRequest(BaseModel):
    irn: str
    auth_token: str

@app.get("/api/irn/{irn}")
async def check_irn(irn: str, auth_token: str):
    result = get_irn_status(irn, auth_token)
    if result.get("status") == "0":
        raise HTTPException(status_code=404, detail="IRN not found")
    return result

@app.get("/api/ewb/{ewb_number}")
async def check_ewb(ewb_number: str, auth_token: str):
    result = get_ewb_status(ewb_number, auth_token)
    return result
```

---

## 4. Sandbox Testing

Both NIC APIs have sandbox environments:
- IRP Sandbox: `https://einvoice1-sandbox.nic.in`
- EWB Sandbox: `https://ewaybillgst.gov.in/api-sandbox`

Register at [einvoice1.gst.gov.in](https://einvoice1.gst.gov.in/Others/apiSandbox) for sandbox credentials.
