# AI-Powered Facebook Reel Ad Automation (n8n)

An n8n workflow that turns product rows in Google Sheets into short-form Facebook Reel ad assets. It uses an LLM to generate structured ad copy, Creatomate to render a 10-second vertical video, and Gmail to prepare a review draft containing the video link and caption.

> **Portfolio/demo project:** This repository contains a sanitized workflow export. It does not include API keys, OAuth credentials, private spreadsheet IDs, or account-specific instance identifiers. You must connect your own accounts and configure your own template before running it.

## What it does

1. **Manual trigger** — starts the workflow when the operator clicks Execute.
2. **Google Sheets** — fetches the first product row where `Posted` is `NO`.
3. **Claude via n8n** — generates a JSON copy package using the product title, price, and link:
   - short on-screen hook and product details
   - product name, price, and CTA
   - Roman Urdu/English caption, CTA caption, and hashtags
4. **JavaScript Code node** — extracts and parses the model's JSON, handles line breaks, and trims on-screen text to configured character limits.
5. **Creatomate** — maps product image and generated text into a 9:16, five-scene Reel template and starts a render.
6. **Set node** — prepares the rendered video URL and social caption.
7. **Gmail** — creates an email draft for human review.
8. **Google Sheets** — updates the matching product row.

## Workflow overview

`Manual Trigger → Google Sheets → Claude → JavaScript JSON Parser → Creatomate Render → Set Facebook Data → Gmail Draft → Google Sheets Update`

## Tech stack

- n8n
- Google Sheets
- Anthropic Claude (through the n8n integration)
- JavaScript (n8n Code node)
- Creatomate
- Gmail

## Requirements

- A running n8n instance
- Google Sheets OAuth credentials
- Anthropic credentials / access to a supported Claude model in your n8n setup
- Creatomate API credentials and your own compatible Reel template
- Gmail OAuth credentials
- A Google Sheet with product data

## Expected Google Sheets columns

The workflow expects these fields:

| Column | Purpose |
|---|---|
| `Product_ID` | Unique product identifier used when updating the row |
| `Title` | Product title supplied to the copywriting model |
| `Price` | Product price |
| `Image_URL` | Publicly accessible product image URL |
| `Product_Link` | Product page URL included in the CTA caption |
| `Posted` | Filter field; workflow looks for `NO` |

The Google Sheets node is configured to return the first matching row where `Posted` is `NO`.

## Setup

1. Import `workflow.json` into your n8n instance.
2. Create/connect your Google Sheets, Anthropic, Creatomate, and Gmail credentials in n8n.
3. In both Google Sheets nodes, select your own spreadsheet and worksheet.
4. In the Creatomate node, select your own template. The template should contain the image and text element names referenced by the workflow (for example, `Image-1`, `Text-1`, etc.).
5. Confirm that the template is vertical (9:16), has five scenes, and matches the field mapping in the Creatomate node.
6. Review the model prompt and replace the demo brand wording with your own brand details if needed.
7. Test with one sample product row before using real products.

## Important accuracy and publishing note

**This workflow generates and renders a Reel and creates a Gmail draft. It does not publish directly to Facebook.** There is no Facebook publishing node in this exported workflow.

Also, the final Google Sheets node currently writes `Posted = YES` after the Gmail draft is created. That status can be misleading because a draft is not the same as a published post. For a production workflow, change the column/status to something like `Draft Created`, or update `Posted = YES` only after a separate verified Facebook publishing step.

## Limitations / things to verify

- The workflow processes only the first matching unposted row per manual execution.
- The Creatomate template and element names must match the mappings in the workflow.
- The LLM output is parsed and length-trimmed, but generated copy should still be reviewed for factual accuracy before publishing.
- Product image URLs must be accessible to Creatomate.
- API usage may incur charges depending on your provider plans.
- Credentials and service access are intentionally not included in this repository.

## Repository contents

- `workflow.json` — sanitized n8n workflow export.
- `README.md` — project overview, requirements, setup, and limitations.

## Portfolio note

This repository demonstrates a personal automation project built with n8n, LLM-based structured content generation, API integrations, JavaScript data handling, and automated media rendering. Describe it as a personal/demo project unless you have separate permission and evidence to present it as paid client work.
