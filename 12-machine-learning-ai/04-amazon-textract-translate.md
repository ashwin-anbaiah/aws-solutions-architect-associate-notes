# Amazon Textract and Amazon Translate

## Amazon Textract — Document Data Extraction

### What Is Textract?
- **Amazon Textract** — automatically extracts **printed text, handwriting, forms, tables, and structured data** from scanned documents.
- Goes far beyond simple OCR (Optical Character Recognition) — it understands **document layout** and extracts related, structured data.

### Key Capabilities
- **Text extraction** — printed and handwritten text from any scanned document or image.
- **Form extraction** — identifies key-value pairs in forms (e.g., "Name: John Doe," "Date: 2026-01-01").
- **Table extraction** — identifies and extracts table structures with rows and columns.
- **Document understanding** — understands layout, multi-column text, signatures, checkboxes.
- **Queries** — ask natural language questions about a document (e.g., "What is the patient's date of birth?").

### Use Cases

| Industry | Use Case |
|---|---|
| **Financial Services** | Extract data from invoices, receipts, financial reports |
| **Healthcare** | Process medical records, insurance claims, prescription forms |
| **Public Sector** | Digitize tax forms, passports, ID documents |
| **Legal** | Extract clauses and data from contracts |

### Example Output (Passport)
```json
{
  "Document ID": "P777777777",
  "Name": "JANE A SAMPLE",
  "SEX": "F",
  "DOB": "01-01-83"
}
```

---

## Amazon Translate — Language Translation

### What Is Translate?
- **Amazon Translate** — fluent and accurate neural machine translation between languages.
- Supports **75+ languages** (and growing).

### Key Features
- Near-real-time translation with high accuracy using deep learning.
- Supports **batch translation** (translate large volumes of documents stored in S3).
- **Custom terminology** — ensure brand names, technical terms, and product names are translated correctly.
- **Active Custom Translation** — fine-tune translation to match your style and domain.

### Use Cases
- Translate user manuals, product descriptions, and documentation
- Localize websites and mobile apps for global audiences
- Real-time translation in customer support chat
- Translate books, articles, and legal documents

---

## Textract vs Translate — Comparison

| Feature | Textract | Translate |
|---|---|---|
| Input | Scanned documents, images, PDFs | Text in any language |
| Output | Extracted structured text/data | Translated text |
| Purpose | Document digitization and data extraction | Language translation |
| Use case | Process forms, invoices, IDs | Localize content, multilingual support |

---

## Key Points / Exam Tips

- **Trigger:** "extract text from scanned documents, forms, invoices, IDs" → **Amazon Textract**
- **Trigger:** "beyond OCR — extract form fields, tables, key-value pairs from documents" → **Textract**
- **Trigger:** "translate text between languages" → **Amazon Translate**
- **Trigger:** "process medical forms, passports, tax forms automatically" → **Textract**
- Textract is NOT a translation service — use Translate for language conversion
- Translate is NOT a document scanning service — it works on plain text input
