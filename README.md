# Email Job Application Automation (`fin-app`)

An automated CLI tool to send personalized job application emails with your attached resume/CV to recruiters. Supports both the official **Gmail REST API (OAuth 2.0)** and standard **SMTP (TLS / App Passwords)** with built-in anti-spam rate limiting and dry-run preview capabilities.

---

## 🌟 Key Features

- **Personalized Email Templates**: Dynamically substitutes company names and recruiter contact names into custom email templates.
- **Smart Domain Extractor**: Automatically infers company names from email domain addresses (e.g., `recruiting@acme-corp.com` $\rightarrow$ `Acme Corp`).
- **CV / Resume Attachments**: Supports attaching `.pdf` and `.docx` files with correct MIME types.
- **Safety Dry-Run Mode**: Preview all generated subject lines, recipients, and bodies before sending real emails.
- **Rate-Limiting & Delay Dispatch**: Throttles email delivery (default: 30s delay) to comply with email provider sending limits and avoid anti-spam triggers.
- **Dual Sending Modes**:
  - **Gmail REST API**: Secure OAuth 2.0 flow using `credentials.json` & token storage.
  - **SMTP**: Direct TLS sending using App Passwords.
- **Detailed Logging**: Logs full audit trails to `send_log.txt` and stdout.

---

## 🛠️ Project Structure

```
fin-app/
├── send_applications_gmail_api.py  # Primary script (Gmail REST API + OAuth2)
├── send_applications.py            # Alternative script (SMTP Delivery)
├── message_template.txt            # Personalized email body template
├── generic_message.txt              # Fallback body template for generic domains
├── example.env                      # Sample environment variables config
├── pyproject.toml                   # Project dependencies & metadata
├── uv.lock                          # Locked dependency versions
└── README.md                        # Documentation
```

---

## 🚀 Quick Setup & Installation

### 1. Prerequisites
- Python `>= 3.13` (or Python 3.9+)
- [`uv`](https://github.com/astral-sh/uv) (recommended) or standard `pip`

### 2. Install Dependencies

Using `uv` (recommended):
```bash
uv sync
```

Or using standard `pip`:
```bash
pip install -r <(uv pip compile pyproject.toml)
# OR manually:
pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib
```

---

## ⚙️ Configuration

### 1. Recipient List (`emails.txt`)
Create an `emails.txt` file in the project root directory. Format each line with pipe `|` separators:

```text
# Format: email | company_name | recruiter_person_name
hr@techsolutions.io | Tech Solutions | Ahmed
careers@startup.com | Startup | HR Team
recruiter@gmail.com | | Sir/Madam
```

> **Note:** If `company_name` is omitted, the script automatically attempts to extract the company name from the email domain.

### 2. Choose Authentication Method

#### **Option A: Gmail REST API (Recommended)**
1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Create a project and enable the **Gmail API**.
3. Set up the OAuth Consent Screen and add your email under **Test Users**.
4. Go to **Credentials** $\rightarrow$ **Create Credentials** $\rightarrow$ **OAuth Client ID** (Application type: *Desktop App*).
5. Download the credentials JSON, rename it to `credentials.json`, and place it in the project root folder.

#### **Option B: SMTP (App Passwords)**
Copy `example.env` to `.env` or export environment variables:
```bash
export SMTP_HOST=smtp.gmail.com
export SMTP_PORT=587
export SMTP_USER=your_email@gmail.com
export SMTP_PASS="your_app_password"   # 16-character Google App Password
export SENDER_NAME="Your Name"
```

---

## 💻 Usage

### 1. Always Run a Dry Run First (Preview Mode)
Verify all rendered messages, subjects, and recipient mappings without sending actual emails:

**Using Gmail API:**
```bash
uv run send_applications_gmail_api.py --emails emails.txt --cv resume.pdf --dry-run
```

**Using SMTP:**
```bash
python send_applications.py --emails emails.txt --cv resume.pdf --dry-run
```

### 2. Send Real Email Applications
Once the dry-run output looks correct, run the script without `--dry-run`:

**Using Gmail API:**
```bash
uv run send_applications_gmail_api.py --emails emails.txt --cv resume.pdf
```
*(On first run, a browser window will open asking you to authorize the application. OAuth tokens will be saved to `token.json` for future runs).*

**Using SMTP:**
```bash
python send_applications.py --emails emails.txt --cv resume.pdf
```

---

## 🛡️ Security & Privacy Notice

The following files contain private data/credentials and are excluded from Git via `.gitignore`:
- `.env` & `*.env` (Environment secrets)
- `credentials.json` & `token.json` (OAuth credentials)
- `emails.txt` & `sent.txt` (Recipient lists & logs)
- `*.pdf` / `*.docx` (Resumes / CVs)
- `send_log.txt` (Execution logs)

Never commit sensitive credential files to public repositories.
