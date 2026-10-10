# Trainer guide — Invoice V2

Use this practical sequence to explain how an AP invoice moves from a downloaded PDF to a reviewed accounting-entry pack. This material is for educational sharing with AICA participants; it does not claim official endorsement.

## Before the class

1. Complete [START_HERE_AICA.md](START_HERE_AICA.md), import the generated workflow and rehearse on the trainer's own Windows machine.
2. Check that Docker, n8n, Ollama and the V2 approval page are running. Confirm the installed model is `qwen2.5:7b`.
3. In the workflow's **Demo Settings**, check the company and as-of date. The supplied invoices use **Northstar Precision Components Pvt Ltd**. A blank as-of date uses the current date in Asia/Kolkata. Use a deliberate training date when demonstrating time-sensitive findings.
4. Use a fresh training database for the first demonstration. If the invoices have already been processed, they will be recognised on another run. Deleting reports does not reset this history.
5. Before the first run, create a full backup of the empty training application and retain it as the starting snapshot for future classes. Do not restore over records you need without first backing them up.
6. Rehearse Outlook separately if email is part of the session. Use your own test recipient and check the actual result. The included helper fixes still require acceptance on your particular Windows/Outlook setup.

The original full manual contains paths from the original Windows installation. Replace those paths with the participant's own n8n-mounted folder; keep the child folder name `invoice_demo_v2`.

## Demonstration sequence

| Step | Show the participants | Teaching point |
|---|---|---|
| 1. Receive invoices | Copy only the 20 PDFs directly inside `sample_invoices` into the installed `input` folder. Keep answer keys and the `next_batch` PDFs out for now. | AP staff download invoices and place them in the agreed folder. |
| 2. Run the batch | Execute the imported V2 workflow in n8n. | n8n registers the batch, extracts PDF text and calls local Qwen. Python saves and checks the results. |
| 3. Review the register | Open `http://localhost:8787`, click Refresh and inspect Invoice register and Batch history. | Persistent invoice IDs and batch/lot IDs support follow-up. A completed extraction is not approval. |
| 4. Compare source and data | Open an invoice's Review screen and source PDF. Compare vendor, invoice number, date, taxes, total and line items. | AI output must be checked against the document. |
| 5. Make decisions | Approve a complete reviewed invoice, hold another and return one for correction with clear reasons. | Human decisions determine which invoices are ready for entry. |
| 6. Produce the pack | Open Reports & email; create/download Approved Entries and Outstanding Invoices. Inspect the Batch Summary too. | The accounting team receives a reviewed entry pack and a separate list of unresolved work. |
| 7. Add the next lot | Copy the five PDFs from `sample_invoices\next_batch` into `input` and run the workflow again. | New invoices join the same database while earlier outstanding work remains visible. |
| 8. Demonstrate duplicates | Leave the previously processed PDFs in `input` and run another batch. | Successful unchanged PDFs are recognised and skipped; receipts are logged without creating fresh invoice records. |
| 9. Show correction/history | Make an evidence-based extraction correction with a reason; inspect the new version and reapprove if appropriate. | Corrections preserve history and require a fresh decision. |
| 10. Optional email/manual entry | Send a selected workbook through Classic Outlook, then demonstrate manual accounting entry and recording its voucher reference. | Email submission and accounting entry are different events. Neither happens merely because an invoice was approved. |

Do not promise a fixed processing time: model speed and extraction results depend on the PC and installed setup. A failed extraction can be retried on a later run and may appear again rather than being skipped as a successfully processed duplicate.

## Useful invoices in the initial pack

| PDF | Discussion |
|---|---|
| `02_office_supplies_pinecrest.pdf` | Start with a straightforward invoice and verify its extracted values. |
| `07_equipment_vector.pdf` | Discuss capitalisation and the choice of ledger. |
| `15_electrical_materials_lumen.pdf` | Deliberate printed-total mismatch of INR 700.00. Show why arithmetic checks matter. |
| `18_equipment_hire_meridian_hire.pdf` | Includes a refundable security deposit; accounting treatment needs review. |
| `19_it_services_harbour.pdf` | Due date is not stated; the system should not invent one. |

Trainer answer keys are in `sample_invoices\expected_results.csv` and `expected_invoice_data.json`. Keep them outside `input`; the model should read the PDFs, not the answer key. Other review findings may depend on the extraction, configured checks and training date.

## Five new invoices for the second batch

All five are single-page portrait PDFs with selectable text and new vendor/invoice identities.

| File | Invoice number | Printed total (INR) |
|---|---|---:|
| `21_Papertrail_Archive_Ledger_Invoice.pdf` | PAS/26-27/1041 | 12,154.00 |
| `22_Sundial_Pneumatics_Parts_Invoice.pdf` | SP/26-27/1042 | 43,447.60 |
| `23_Aavrit_Connectivity_Account_Invoice.pdf` | ABC/26-27/1043 | 24,780.00 |
| `24_Neelvan_Testing_Field_Form_Invoice.pdf` | NET/26-27/1044 | 31,801.00 |
| `25_Rookline_Media_Service_Cards_Invoice.pdf` | RMW/26-27/1045 | 43,778.00 |
| **Total** | | **155,960.60** |

If all initial invoices were successfully processed and remain in `input`, the next run should recognise those 20 and register these five as new. Actual extraction quality still needs verification. The app reads PDFs directly in `input`; copying only a subfolder into `input` does not add its contents to the batch.

## Explain the decisions clearly

- **Approve for manual entry:** current facts and accounting lines have been reviewed. Raw status: `approved_awaiting_entry`.
- **Put on hold:** unresolved work remains visible across batches; include what is required and who should follow up in the remarks.
- **Return for correction:** a corrected document or corrected extraction is expected. It remains outstanding.
- **Reject & close:** the case leaves outstanding work but remains in history. Reopen if it later needs correction.
- **Use corrected invoice:** process a genuinely corrected PDF first, then link it from the original outstanding record. Both documents remain, the original receives a new version, and fresh approval is required.
- **Mark manually recorded:** use after entry in the accounting system and capture the real company, voucher type, number and date. For a classroom simulation, use a clearly identified test company/reference.

A correction to an amount or category does not automatically rebuild the accounting lines. Review the **Accounting entry** tab as well. Never alter a correctly extracted printed amount merely to conceal an error on the supplier document; use a proper corrected invoice for that scenario.

## Reports and email

Batch completion generates Batch Summary and Outstanding Invoices workbooks. Create Approved Entries after reviewing invoices. These workbooks include multiple sheets, including invoice and line-item information; V2 does not automatically attach three separate workbooks to one email.

An approved export reserves its invoice versions. If another copy is needed, re-download the same export. Use Release reservation only for an unposted pack that has been withdrawn from use. A correction can supersede an old export, so check that the pack is current before entry or sending.

For the email demonstration:

1. Open **Classic Outlook**. With the sending helper stopped, run `outlook_bridge\CHECK_OUTLOOK_V2.cmd` to check the exact sender account.
2. Run `outlook_bridge\START_OUTLOOK_V2.cmd` and keep its window open.
3. In Reports & email, choose a current workbook and **Email via Outlook**. Verify sender, recipients and attachment before submitting.
4. Inspect the application result and Outlook Sent Items/Outbox. `queued` means waiting; `submitted` means Outlook accepted the send call, not delivery proof.
5. A job expires after three minutes without pickup. For an unknown result, inspect Sent Items, Outbox and Drafts before sending again.

Send the current approved pack before marking its invoices manually recorded, because the email action revalidates its current approval/export state. No automatic vendor rejection emails, action calls or reminders are generated.

## Close the demonstration

Show the same invoice's decision and version history, the later batch's duplicate receipts, the outstanding carryforward and a backup ZIP. Explain that the SQLite database retains this history even after the browser or Docker is restarted.

For another class, restore the deliberately saved empty training backup only after stopping the V2 workflow and closing the Outlook helper; retain a backup of any session records you need first. Do not delete Docker volumes as a classroom shortcut. Restoring replaces the application's recorded state, archived documents and reports; it is not just a screen refresh.

## State the current boundaries

Selectable-text PDFs only; no OCR. One company and one reviewer. Local model output is an extraction suggestion, not approval. No automatic Tally posting, payment, live GST validation, mailbox intake or multiuser task assignment. Review exceptions, source evidence and ledger judgments remain with the person operating the system.
