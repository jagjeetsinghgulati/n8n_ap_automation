# Invoice Approval and Tracking — AICA Training Project

A local Windows demonstration of accounts-payable automation using **n8n, Ollama/Qwen, Python, SQLite and Classic Outlook**.

## Download the complete project

**[Download AICA_Invoice_Project_GitHub_Ready.zip](AICA_Invoice_Project_GitHub_Ready.zip?raw=true)**

The ZIP is the complete distribution: **69 files, including all application source code, setup scripts, the n8n workflow template, the user manual and 25 fictional PDF invoices**. Source files are packaged inside the ZIP with their required folder structure. Extract it before running setup.

- [Installation guide](START_HERE_AICA.md)
- [Trainer demonstration guide](TRAINER_GUIDE.md)
- [Full user manual and 25 FAQs (PDF)](Invoice_V2_User_Manual_and_Trainer_FAQ.pdf)

## What the project demonstrates

1. Copy vendor invoices into a local input folder.
2. Use n8n and a local Qwen model to extract invoice particulars and line items.
3. Keep batches, documents and invoice versions in a local SQLite database.
4. Identify duplicates and amount/accounting discrepancies.
5. Review invoices, approve them, hold them, return them for correction or reject/close them with reasons.
6. Generate Batch Summary, Outstanding Invoices and Approved Entries Excel reports.
7. Optionally send one selected report workbook through Classic Outlook on Windows.
8. Enter approved data manually in the accounting system and record the voucher reference in the approval application.

## Installation in brief

The project extends an existing Windows setup with Docker Desktop/WSL 2, a running n8n container named `n8n`, and Ollama with `qwen2.5:7b`. n8n must have a Windows folder mounted at `/files` and use a user-defined Docker bridge network. Classic Outlook is optional for email.

1. Download and extract the **project ZIP linked above**. If you use GitHub's **Code → Download ZIP**, extract that repository download first, then extract the enclosed `AICA_Invoice_Project_GitHub_Ready.zip`.
2. Inside `AICA_Invoice_Project`, copy the entire **`invoice_demo_v2`** folder into the Windows folder already mounted to n8n as `/files`. Example: `C:\n8n-files\invoice_demo_v2`. Use your own mount source and keep the child folder name unchanged.
3. Start Docker Desktop, the existing n8n container and Ollama, then double-click `SETUP_V2.cmd` inside `invoice_demo_v2`.
4. Choose your reviewer username. Setup generates your own credentials, starts the Python service and creates the SQLite database on first startup.
5. Import the generated **`Ready_To_Import_V2.json`** into n8n as a new workflow. Do not import the placeholder template directly or replace an existing V1 workflow.
6. Open the approval application at **http://localhost:8787** and sign in using the generated `LOCAL_LOGIN_PRIVATE.txt`. n8n normally remains at **http://localhost:5678**.

No separate Windows Python or SQLite installation is required for V2. GitHub distributes the files; processing and approval run on each participant's local computer. The included manual uses the original trainer's Windows path as an example; replace it with your own mount location. Historical V1 reference files described in that manual are omitted from this distribution.

## Training invoices

The package contains 20 initial fictional vendor invoices under `invoice_demo_v2/sample_invoices/` and five additional portrait PDFs under `sample_invoices/next_batch/`.

Copy PDF files into the top level of `input`; leave the trainer answer keys out. Process the first batch, make approval decisions, then add the five new PDFs and run again. The application retains earlier outstanding cases. Successfully processed unchanged PDFs are recognised as duplicate receipts on subsequent runs. Deleting Excel reports does not clear this history.

## Scope and review

- Selectable-text PDFs only, one invoice per PDF. OCR is not implemented.
- One company workspace and one named reviewer; ordinary INR supplier invoices.
- Extracted figures, suggested ledgers and accounting entries require human review.
- Tally entry is manual. The application does not automatically post to or verify Tally, make payments, download email attachments, or send automatic vendor follow-ups.
- Credit notes, foreign currency, automatic TDS, split payables and inventory posting need separate handling.
- Outlook sending uses Classic Outlook and the separate Windows helper. Verify it on your own machine with a test recipient. A `submitted` result is not proof of delivery.

The source and launchers match the reviewed V2 runtime. The distribution includes its original automated-test records; extraction accuracy and Windows/Outlook operation must be checked during the local demonstration.

## Local records and credentials

The shared package contains no live database, real invoice history, generated email requests or installation credentials. Each participant creates their own local records. Keep `.env`, `LOCAL_LOGIN_PRIVATE.txt`, `Ready_To_Import_V2.json`, live input/archive/output/backups and database files out of GitHub.

Use the application backup function or `BACKUP_V2.cmd` for a consistent database/document backup. Read the manual before restoring a backup over current records.

Prepared for educational sharing with AICA learners and trainers. All supplied invoices are fictional. This repository does not claim official AICA or ICAI endorsement.
