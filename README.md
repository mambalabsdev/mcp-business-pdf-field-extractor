# Business PDF Field Extractor MCP Server

[![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-business-pdf-field-extractor)](https://www.npmjs.com/package/@mambalabsdev/mcp-business-pdf-field-extractor) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-business-pdf-field-extractor)](https://github.com/mambalabsdev/mcp-business-pdf-field-extractor/blob/main/LICENSE)

An MCP server for the Mamba Labs Business PDF Field Extractor actor on Apify. Read invoices, purchase orders, and contracts from PDFs as fixed fields.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Give PDF addresses. The actor reads each file's text, or runs OCR on scanned pages, works out whether it is an invoice, a purchase order, or a contract, and returns fixed fields. Invoices and purchase orders return numbers, dates, parties, totals, line items, and a check that the amounts add up. Contracts return parties, dates, term, renewal, notice days, governing law, and which standard clauses appear. It uses labels and column position with no AI, so a value with no known label stays null and is listed as missing.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-business-pdf-field-extractor": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-business-pdf-field-extractor"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `extract_business_pdf_fields` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Extract the fields from this invoice PDF: <address>"
- "Read these purchase orders and list the line items: <addresses>"
- "What is the renewal and notice wording in this contract PDF: <address>"

## Tool

`extract_business_pdf_fields`. One tool. Example arguments:

```json
{
  "pdf_urls": [
    "https://docs.oasis-open.org/ubl/cd-UBL-1.0/fs/Order/pdf/OfficeOrder.Example-us.pdf",
    "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf",
    "https://gba.georgia.gov/document/publication/services-contractsample-agreement-templatepdf/download"
  ]
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults.

| Input | Type | Required | Description |
|---|---|---|---|
| `pdf_urls` | array | no | Web addresses of PDF files, up to 1,000. Each file up to 40 MB. The address must answer without a login. In Clay, put the column in a one item list. |
| `pdf_file` | string | no | Optional. One PDF file from your computer, stored in your Apify account. |
| `document_type` | string | no | auto (default) works it out per file. Set invoice, purchase_order, or contract to read every file as that type. |
| `date_order` | string | no | How to read a date such as 03/04/2025 when both readings are valid. mdy (default) reads March 4, dmy reads 3 April. Unambiguous dates ignore this. |
| `read_scans` | boolean | no | yes (default): a page with no text layer goes through OCR, charged per page. no: those pages are skipped and the row says so. |
| `ocr_language` | string | no | Language of scanned pages: eng (default), deu, fra, spa, nld, ita, or por. |
| `max_pages` | number | no | Pages to read per file, up to 500. Default 50. |
| `max_ocr_pages` | number | no | Scanned pages to read per file, up to 100. Default 10. This caps the OCR charge per file. |
| `max_documents` | number | no | Files to read in this run, up to 1,000. Default 100. |
| `line_item_rows` | boolean | no | yes (default): each line item is also its own free row, which suits CSV and Clay. no: line items stay in the line_items field only. |
| `include_text` | boolean | no | yes: add the document text to the row, up to 200,000 characters. Default no. |
| `max_charge_usd` | string | no | Optional. The run stops before a charge that would pass this amount. Summary rows are always returned. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `document-row`, one PDF with at least one field read | $0.01 | $0.0095 | $0.009 | $0.0085 |
| `ocr-page`, one scanned page read by OCR, charged when OCR returned text | $0.014 | $0.0133 | $0.0126 | $0.0119 |
| `apify-actor-start`, per run start | $0.003 | $0.003 | $0.003 | $0.003 |

Line item rows, documents with no fields read, failed downloads, and refused input are free. Mamba Labs pays the platform costs of the run, Apify proxy traffic included.

## Full actor documentation

[apify.com/mambalabs/business-pdf-field-extractor](https://apify.com/mambalabs/business-pdf-field-extractor)

## License

MIT. See [LICENSE](LICENSE).
