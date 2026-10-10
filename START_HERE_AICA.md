# Start here: Invoice V2 for AICA training

This repository shares a Windows local invoice-processing demonstration. It does not start an online service on GitHub. Your invoices, approvals and database are created on your computer when you install and run it.

## Before setup

- Windows with Docker Desktop running Linux containers through WSL 2.
- A working n8n Docker container named `n8n`, with one Windows folder bind-mounted to `/files`, attached to a user-defined Docker bridge network.
- Ollama on Windows and the `qwen2.5:7b` model downloaded. The workflow calls Ollama through `http://host.docker.internal:11434`.
- Classic Outlook for Windows with your account configured, only if demonstrating email. The new Outlook app is not supported by the helper.

If n8n already works, keep its current configuration. The V2 setup detects its mount and network. This package does not install n8n, Ollama, WSL or Docker for you. Consult the included user manual for those prerequisites.

## Install the project

1. Download the repository ZIP and extract it.
2. Identify the Windows folder currently mounted to n8n's `/files`. Example: if the Windows folder is `C:\n8n-files`, copy the repository's **entire `invoice_demo_v2` folder** into it. The result must be `C:\n8n-files\invoice_demo_v2`.
3. Keep the folder name exactly `invoice_demo_v2`. Do not run setup from the repository download root or from inside the ZIP. Do not copy it over your original `invoice_demo` folder.
4. Start Docker Desktop, the existing n8n container, and Ollama.
5. Double-click `invoice_demo_v2\SETUP_V2.cmd`. Enter your own reviewer username when prompted. The original script offers `jagjeet` as its default; you may type another name. Setup downloads the Python container image if needed, generates credentials, starts the application and creates a SQLite database on first startup.
6. Read the newly generated `LOCAL_LOGIN_PRIVATE.txt`. Keep this file, `.env` and `Ready_To_Import_V2.json` private.
7. Open [n8n](http://localhost:5678), import the newly generated `Ready_To_Import_V2.json` as a **new** workflow, and save it. The JSON under `workflows/` is a setup template and is not imported directly.
8. Open the approval application at [localhost:8787](http://localhost:8787) and use your new reviewer login.

The included PDF manual refers to the original trainer's Windows path. Replace that example path with your own `/files` mount source. The account name and path in the manual are examples, not shared credentials.

## First run

1. Copy a few PDFs from `sample_invoices` into the new `input` folder. Copy PDFs only; leave answer keys with the trainer. Each PDF must contain one invoice with selectable text.
2. In n8n, review the V2 configuration node. The synthetic buyer is `Northstar Precision Components Pvt Ltd`. The default model is `qwen2.5:7b`; the as-of date defaults to the current India date. If reproducing a training snapshot, use a suitable explicit date after checking the invoice dates.
3. Execute the V2 workflow and wait for completion.
4. Refresh the approval application. Review invoice details, checks, accounting entries and the source PDF before recording a decision.
5. Download the batch/outstanding reports. Generate an approved-entries report after approving eligible invoices.
6. For a later run, copy the five PDFs from `sample_invoices\next_batch` into `input`. The workflow scans the top level of `input`; it does not process this sample subfolder directly.

Re-running unchanged successfully processed files records duplicate receipts and skips extraction. Deleting Excel files or renaming PDFs does not reset history. A failed extraction can be retried. Keep corrected invoices linked to the original case using the application's correction workflow.

## Optional Outlook demonstration

Open Classic Outlook and run `outlook_bridge\CHECK_OUTLOOK_V2.cmd` with any running helper closed. Verify the sender account, then start `START_OUTLOOK_V2.cmd` and keep its window open. Choose a generated report in the approval application, review the sender and recipient addresses, and submit its email request. Each request attaches one selected workbook. Confirm the result in the application and Outlook Sent Items/Outbox. A submitted result is not delivery confirmation.

The sender-selection fix is included. Sending must still be verified on each participant's Windows/Outlook setup. Do not use real vendor recipients for an unreviewed training run.

## What stays local

The approval container runs Python 3.12. Python's `sqlite3` support provides the embedded database; no separate Windows Python or SQLite installation is required for V2. The database is `/data/invoice_control_v2.db` inside the approval container, in the Docker volume `invoice_v2_data`. Your n8n database is separate.

Generated folders include `input`, `archive`, `output` (including the Outlook queue) and `backups`. None of their live contents is included in this sharing copy. The application does not import the trainer's invoice history or approvals.

## Daily operation and limits

Use `START_V2.cmd`, process invoices in n8n, then review at port 8787. `STOP_V2.cmd` stops the approval application. `BACKUP_V2.cmd` creates a full V2 backup; `RESTORE_V2.cmd` replaces the current V2 records with a selected backup after explicit confirmation. See the manual before restoring.

This version has one reviewer and one company workspace. It supports ordinary INR supplier invoices. It has no OCR, automatic Tally posting or Tally verification. Credit notes, foreign currency, TDS/split payables and inventory posting need separate handling. Ledger suggestions and extracted figures must be reviewed by a person. Email is for sending a selected report; automatic vendor reminders or approval/rejection letters are not implemented.

The full PDF manual describes the original V2 bundle, including a `reference_v1` folder. That optional historical folder is omitted from this AICA sharing copy. Keep any existing V1 workflow and configuration separately; V2 setup does not change them.
