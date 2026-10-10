# Local Invoice Approval and Tracking — V2

A Windows-local training project for demonstrating invoice extraction, accounting review and approval tracking with **n8n, Ollama/Qwen, Python and SQLite**. Prepared for sharing with AICA learners and trainers; this repository does not claim official AICA or ICAI endorsement.

PDF invoices are processed by a local language model, checked, retained with their history, reviewed by a person and exported to Excel for **manual accounting entry**. Optional email uses the user's Classic Outlook account on Windows.

## What the demonstration covers

- Different vendor PDF layouts, invoice particulars and line items.
- Batch/lot history and persistent invoice records across runs.
- Exact-file duplicate recognition and possible business-duplicate checks.
- Amount and accounting checks, with review reasons and decisions.
- Approve, hold, return for correction, reject/close and link a corrected invoice.
- Version history, reviewer details and outstanding items carried into later batches.
- Batch Summary, Outstanding Invoices and Approved Entries Excel workbooks.
- Optional Outlook sending of one selected report workbook per email.
- Manual voucher-reference confirmation and application backup/restore.

**Start here:** [AICA installation guide](docs/START_HERE_AICA.md) · [Trainer demonstration guide](docs/TRAINER_GUIDE.md) · [Full user manual and 25 FAQs](docs/Invoice_V2_User_Manual_and_Trainer_FAQ.pdf)

## What you need first

This V2 package extends an **existing working local n8n setup**. It does not install Docker, n8n or Ollama for you.

| Component | Required arrangement |
|---|---|
| Windows and Docker Desktop | Docker running with its Linux-container/WSL2 setup available |
| Existing n8n | Running Docker container named `n8n` |
| Shared invoice folder | Exactly one Windows bind mount into n8n at `/files` |
| Docker network | n8n connected to a user-defined bridge network |
| Ollama on Windows | `qwen2.5:7b` already downloaded and reachable from n8n at `http://host.docker.internal:11434` |
| Windows PowerShell | Local scripts permitted by your device's policy |
| Classic Outlook | Optional; required only for the Windows email helper |

Setup uses the `python:3.12-slim` Docker image. Python runs the approval application, checks, reports and database operations. SQLite is accessed through Python; a separate Windows Python or database-server installation is not required for normal use. Initial downloads need the internet; local extraction/review can then operate locally. Outlook sending needs an online account.

## Install your own copy

1. Download and extract this repository. Keep your existing n8n configuration and any V1 workflow backed up.
2. Copy the complete **`invoice_demo_v2` folder** into the Windows folder already mounted as `/files` in n8n. Keep the folder name unchanged.
3. Start Docker Desktop, your existing `n8n` container and Ollama.
4. Run `invoice_demo_v2\SETUP_V2.cmd`. Choose your own reviewer login when prompted; pressing Enter uses the package's original training default, `jagjeet`.
5. Setup creates a private `.env`, `LOCAL_LOGIN_PRIVATE.txt` and `Ready_To_Import_V2.json`. Keep these files on your own machine and do not upload them to GitHub.
6. In n8n, import **`Ready_To_Import_V2.json` as a new workflow**. Do not import the placeholder template directly or replace a working V1 workflow.
7. Open [the approval application](http://localhost:8787), using the login in `LOCAL_LOGIN_PRIVATE.txt`.
8. Follow the [trainer guide](docs/TRAINER_GUIDE.md) to run the initial 20 invoices, add five new invoices and demonstrate duplicate handling.

For example, if your existing n8n mount is `C:\Training\n8n-files` → `/files`, install at:

```text
C:\Training\n8n-files\invoice_demo_v2
```

**The extracted GitHub repository folder is not the application install folder.** Copy its `invoice_demo_v2` child folder to the mount location. Windows paths in the original manual, including `C:\Jagjeet Singh\n8n-files`, are examples from the original installation; substitute your own existing mount folder. Setup verifies the location and network rather than modifying your original n8n installation.

## Where each part runs

| Part | Location |
|---|---|
| n8n browser interface | `http://localhost:5678` in the original setup; use your configured n8n port if different |
| V2 approval interface | `http://localhost:8787` |
| n8n → V2 API | `http://invoice-approval-v2:8787` on the Docker network |
| n8n → Ollama | `http://host.docker.internal:11434` |
| SQLite database | `/data/invoice_control_v2.db` in the approval container; persisted in Docker volume `invoice_v2_data` |
| Input, archive, reports and backups | Your installed `invoice_demo_v2` folder on Windows |
| Outlook helper | Windows, outside Docker, with Classic Outlook |

The two browser ports serve different applications. GitHub holds the project files; it does not run the invoice system.

## Repository contents

| Path | Purpose |
|---|---|
| `invoice_demo_v2/app/` | Python service, approval screens, checks and Excel reports |
| `invoice_demo_v2/scripts/` and root `.cmd` launchers | Setup, start/stop, status, backup and restore |
| `invoice_demo_v2/compose.v2.yaml` | V2 Docker service, mounts and network connection |
| `invoice_demo_v2/workflows/` | Reusable n8n template with an API-token placeholder |
| `invoice_demo_v2/outlook_bridge/` | Classic Outlook helper and account-selection check |
| `invoice_demo_v2/sample_invoices/` | Initial 20 fictional invoices and trainer answer keys |
| `invoice_demo_v2/sample_invoices/next_batch/` | Five additional portrait PDFs for a later batch |
| `invoice_demo_v2/tests/` | Development checks and fixtures |
| `docs/` | AICA setup notes, trainer flow and full PDF manual |

The runtime folders `input`, `archive`, `output` and `backups` are created during setup. The shared copy intentionally contains no live database, personal credentials, actual invoice records or generated email requests. The `.gitignore` helps keep local runtime files out of later commits; still review every upload.

## Scope and validation

- Use **selectable-text PDFs**, one invoice per PDF. OCR and splitting multiple invoices from one PDF are not implemented. PDF files larger than 25 MB are not accepted; extracted text beyond 24,000 characters is flagged/truncated for model processing.
- The app supports one company and a single named reviewer. Suggested ledgers and model output require human verification.
- Ordinary INR supplier invoices are the supported accounting scope. Credit notes, foreign currency, automatic TDS, split payables and inventory posting need separate handling.
- There is no automatic Tally posting, Tally verification, payment execution, mailbox download or automatic vendor follow-up. Recording a voucher reference is a manual confirmation.
- An approved export reserves the included versions. Re-download the original workbook if another copy is needed; exports are dated snapshots.
- Outlook integration requires a desktop acceptance test. Account-selection fixes are included, but successful sending on every recipient's Windows/Outlook setup is not established. A `submitted` result is not proof of email delivery.

The original package's automated validation scope is documented in [VALIDATION.txt](invoice_demo_v2/VALIDATION.txt). Fixture-based checks do not prove Qwen extraction accuracy; compare actual run results with the source PDFs. The 25 invoices are fictional training material.

## Daily use and recovery

Run `START_V2.cmd`, copy PDFs into `input`, execute the V2 n8n workflow, and review at `localhost:8787`. Use `STATUS_V2.cmd` to check the approval service and `STOP_V2.cmd` to stop it. The Outlook helper is started separately when needed.

Use the application backup action or `BACKUP_V2.cmd` to retain a consistent database plus archived documents and reports. Keep credentials separately and privately. Closing the browser, deleting Excel reports or copying an unchanged PDF again does not reset the database or its duplicate history. Restore only a deliberately selected backup after stopping the workflow and Outlook helper; the full manual explains this process.

The full PDF manual describes the original V2 bundle, including a `reference_v1` folder. That optional historical folder is omitted from this AICA sharing copy. Keep any existing V1 workflow and configuration separately; V2 setup does not change them.
