# 🧾 GST & e-Invoicing AI Assistant

> An intelligent, AI-powered chatbot for Indian GST compliance, e-Invoicing (IRN), and e-Way Bill management — built as a fast, clean single-page application.

![GST Assistant Banner](https://img.shields.io/badge/GST-e--Invoicing%20Assistant-0d7377?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xNCAySDZhMiAyIDAgMCAwLTIgMnYxNmEyIDIgMCAwIDAgMiAyaDEyYTIgMiAwIDAgMCAyLTJWOGwtNi02em0tMSAxdi01bDUgNWgtNXpNNiA3aDJ2Mmgtem0wIDRoMTJ2MUg2em0wIDRoMTJ2Mkg2em0wIDRoNnYySDZ6Ii8+PC9zdmc+)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-ClearTax%20AI-1a3a5c?style=for-the-badge)
![GST](https://img.shields.io/badge/GST-FY%202024--25-27ae60?style=for-the-badge)

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 💬 **AI Chat** | Ask any GST or e-Invoicing question in plain English — powered by LLM |
| 📄 **Document Upload** | Upload PDF, DOCX, CSV, Excel, TXT, JSON and ask questions about the document |
| 🔍 **IRN Status Checker** | Validate IRN format (64-char SHA-256) and get compliance guidance |
| 🚛 **e-Way Bill Checker** | Validate EWB number (12-digit) with compliance and validity information |
| 📋 **Quick Questions** | One-click shortcuts for the most common GST queries |
| 🎨 **Clean UI** | Mild, professional navy-teal design with GST-themed styling |
| ⚡ **Fast** | Single HTML file, no build step required |

---
## 🖼️ Screenshots

### AI Chat
![AI Chat view](Screenshot/AI%20chat%20view.png)

### IRN Status Checker
![IRN Status Checker](Screenshot/IRN%20status.png)
```



## 🚀 Quick Start

### Option 1 — Run Directly in Browser
No installation needed. Just open `index.html` in a browser:

```bash
git clone https://github.com/your-username/gst-einvoicing-assistant.git
cd gst-einvoicing-assistant
open index.html   # macOS
# OR
start index.html  # Windows
```

> **Note:** The AI chat and document analysis features require deployment on the [ClearTax AI Platform](https://cleartax.in/ai) to access the `ctai` bridge SDK. Running locally opens the UI only.

### Option 2 — Deploy on ClearTax AI Platform
1. Upload `index.html` as a file on the ClearTax AI Platform
2. Use `create_app` with the file ID as the asset
3. Set `bridge_permissions` for `read_file`, `upload_file`, and `infer` operations

### Option 3 — Integrate with Your Backend
See the [API Integration Guide](#-api-integration-guide) section below.

---

## 📁 Project Structure

```
gst-einvoicing-assistant/
│
├── index.html              # Main application (self-contained)
├── README.md               # This file
├── requirements.txt        # Python dependencies (for backend integration)
├── .gitignore              # Git ignore rules
├── LICENSE                 # MIT License
│
├── docs/
│   ├── api-integration.md  # IRP & EWB API integration guide
│   ├── irn-format.md       # IRN format specification
│   └── ewb-rules.md        # e-Way Bill rules reference
│
└── examples/
    ├── sample-invoice.json # Sample e-Invoice JSON payload
    └── sample-irn.txt      # Sample IRN for testing
```

---

## 🧠 AI Capabilities

The chatbot is trained on knowledge of:

- **GST Acts** — CGST Act 2017, IGST Act, SGST Acts, UTGST Act and all amendments
- **e-Invoicing** — IRP system, JSON schema v1.1, IRN generation, QR code standard, IRP portals
- **e-Way Bill** — Generation, validity, multi-vehicle, Part-A/Part-B, extension rules
- **Returns** — GSTR-1, GSTR-3B, GSTR-9, GSTR-9C, IFF, QRMP scheme
- **ITC** — Eligibility, Section 17(5) blocked credits, GSTR-2B reconciliation
- **Penalties** — Section 122, 125, 129 — penalties for non-compliance
- **HSN/SAC** — Code classification and applicable GST rates

### Document Analysis
Upload any of these document types and ask questions:
- 📕 **PDF** — GST invoices, tax notices, CA reports
- 📊 **Excel/CSV** — HSN-wise sales data, ITC ledger, turnover sheets
- 📄 **DOCX** — GST orders, agreements, contracts
- 📋 **JSON** — e-Invoice payload, GSTR-1 JSON export

---

## 🔍 IRN Format Reference

An IRN is a **64-character SHA-256 hash** computed from:

```
IRN = SHA256(GSTIN_of_Supplier + Document_Type + Document_Number + Document_Date)
```

| Field | Format | Example |
|-------|--------|---------|
| Length | 64 characters | — |
| Character set | Hexadecimal (0-9, a-f) | — |
| Algorithm | SHA-256 | — |
| IRP Portals | NIC, ClearTax, IRIS, Cygnet, EY, GSTN | — |

**Cancellation rules:**
- IRN can be cancelled within **24 hours** of generation
- Cannot be cancelled if linked to an active e-Way Bill
- A cancelled IRN means the invoice is void

---

## 🚛 e-Way Bill Reference

### Threshold
| Transaction Type | Threshold |
|-----------------|-----------|
| Interstate movement | ₹50,000+ |
| Intrastate movement | ₹50,000+ (most states) |
| Handicraft goods | All values (unregistered persons) |

### Validity Period
| Distance | Validity |
|----------|---------|
| Up to 200 km | 1 day |
| 200–400 km | 2 days |
| 400–600 km | 3 days |
| 400–600 km | 3 days |
| Every 200 km thereafter | +1 day |
| ODC (Over-Dimensional Cargo) | 1 day per 20 km |

### Extension Window
- **Within 8 hours before expiry** — can be extended by generator or transporter
- **Within 8 hours after expiry** — can still be extended

---

## 🔌 API Integration Guide

To enable **live** IRN and e-Way Bill status lookups, integrate with NIC APIs:

### IRN Status API (NIC IRP)
```
Base URL: https://api.einvoice1.gst.gov.in
Endpoint: GET /irnstatus/v2/{irn}
Auth: Bearer token (obtain via GSTIN + OTP flow)
```

**Example (Python):**
```python
import requests

def get_irn_status(irn: str, auth_token: str) -> dict:
    url = f"https://api.einvoice1.gst.gov.in/irnstatus/v2/{irn}"
    headers = {
        "Authorization": f"Bearer {auth_token}",
        "Content-Type": "application/json"
    }
    response = requests.get(url, headers=headers)
    return response.json()
```

### e-Way Bill Status API (NIC EWB)
```
Base URL: https://api.ewaybillgst.gov.in
Endpoint: GET /ewayapi/getewbstatus?ewbno={ewb_number}
Auth: Bearer token (GSTIN + OTP authentication)
```

**Example (Python):**
```python
def get_ewb_status(ewb_number: str, auth_token: str) -> dict:
    url = f"https://api.ewaybillgst.gov.in/ewayapi/getewbstatus"
    headers = {"Authorization": f"Bearer {auth_token}"}
    params = {"ewbno": ewb_number}
    response = requests.get(url, headers=headers, params=params)
    return response.json()
```

### Authentication Flow
Both APIs use a two-step auth:
1. **Generate OTP** — POST with GSTIN to `/auth/gettoken`
2. **Verify OTP** — POST OTP received on registered mobile to get Bearer token
3. Tokens are valid for **6 hours**

See full API documentation:
- [NIC IRP API Docs](https://einvoice1.gst.gov.in/Others/apiSandbox)
- [NIC EWB API Docs](https://ewaybillgst.gov.in/api-docs)

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla HTML5, CSS3, JavaScript (ES2020+) |
| Fonts | [Plus Jakarta Sans](https://fonts.google.com/specimen/Plus+Jakarta+Sans), [DM Mono](https://fonts.google.com/specimen/DM+Mono) via Google Fonts |
| AI/LLM | ClearTax AI Platform (`ctai.infer`) — Claude / GPT |
| File Handling | ClearTax AI File Bridge (`ctai.uploadFile`, `ctai.readFile`) |
| Deployment | ClearTax AI App Platform |
| Backend (optional) | Python 3.10+ with `requests`, `fastapi` |

---

## 🔧 Configuration

### Environment Variables (for backend integration)
Create a `.env` file:
```env
# NIC IRP API credentials
IRP_CLIENT_ID=your_client_id
IRP_CLIENT_SECRET=your_client_secret
IRP_GSTIN=your_gstin

# NIC EWB API credentials
EWB_CLIENT_ID=your_client_id
EWB_CLIENT_SECRET=your_client_secret

# Application settings
APP_PORT=8000
APP_ENV=production
```

---

## 📋 Requirements

### Python Backend (optional)
Install dependencies:
```bash
pip install -r requirements.txt
```

### Browser Support
| Browser | Version | Support |
|---------|---------|---------|
| Chrome | 90+ | ✅ Full |
| Firefox | 88+ | ✅ Full |
| Safari | 14+ | ✅ Full |
| Edge | 90+ | ✅ Full |

---

## 📜 Compliance Coverage

This assistant covers:

- ✅ CGST Act, 2017 (as amended)
- ✅ e-Invoicing Notifications — 13/2020-CT (w.e.f. 01-Oct-2020)
- ✅ e-Way Bill Rules — Chapter XVI, CGST Rules 2017
- ✅ Turnover thresholds — current as of FY 2024-25 (₹5 Crore+)
- ✅ IRP portals — all 6 authorised IRPs
- ✅ GSTR filing deadlines and IFF provisions
- ✅ ITC reconciliation — GSTR-2B based matching

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/add-hsn-lookup`
3. Commit your changes: `git commit -m 'Add HSN code lookup feature'`
4. Push to the branch: `git push origin feature/add-hsn-lookup`
5. Open a Pull Request

### Contribution Ideas
- [ ] HSN/SAC code lookup with rates
- [ ] GSTR-1/3B filing deadline calendar
- [ ] ITC calculator
- [ ] GST rate finder by product description
- [ ] Reverse Charge Mechanism checker
- [ ] GST registration eligibility checker

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- [GST Council India](https://gstcouncil.gov.in)
- [NIC IRP Portal](https://einvoice1.gst.gov.in)
- [NIC e-Way Bill Portal](https://ewaybillgst.gov.in)
- [CBIC — Central Board of Indirect Taxes & Customs](https://cbic.gov.in)
- [ClearTax AI Platform](https://cleartax.in)

---

## 📞 Support

- 📧 For platform questions: support@cleartax.in
- 📖 GST Help: [GST Council FAQ](https://gstcouncil.gov.in/faq)
- 🏛️ Official Portal: [gst.gov.in](https://gst.gov.in)

---

> **Disclaimer:** This tool is for informational and educational purposes. Always consult a qualified CA/tax professional for specific legal advice. GST laws are subject to amendment — verify current notifications at [cbic.gov.in](https://cbic.gov.in).
