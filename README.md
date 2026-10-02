# pdfrender API — GitHub Action

HTML and CSS to PDF API and MCP server. WeasyPrint with page headers, footers and page numbers, no headless browser. Hosted in Germany.

pdfrender turns HTML and CSS into PDF over a REST API and an MCP server. WeasyPrint 70 lays out the pages, with paper size, margins, headers, footers and page numbers set in CSS. It runs no JavaScript and fetches no URLs, so images and fonts go in as data: URIs. Limits are 2 MB of HTML and 50 pages per render. The servers are in Germany. The free plan has 100 credits a month and needs no card.

Calls the [pdfrender API](https://pdfrender.dev) from a workflow: every operation, one step each. Jobs are waited for and their result is downloaded.

## Get an API key

[Create a key](https://pdfrender.dev/go/gh-action?to=/app/api-keys) and store it as the repository secret `PDFRENDER_API_KEY`. The key is optional: without `api-key` the action runs on the anonymous tier, with lower limits.

## Usage

```yaml
on: push
jobs:
  pdfrender:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7.0.1
      - uses: pdfrender-dev/pdfrender-action@v1.0.0
        with:
          operation: get_me
          api-key: ${{ secrets.PDFRENDER_API_KEY }}
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `operation` | yes |  | The API operation to call: get_me, render_html_to_pdf. |
| `file` | no |  | Path of the file to upload, for operations that take one. |
| `json` | no |  | The request body as a JSON object, for operations that take one. |
| `query` | no |  | Path, query and form parameters, one key=value per line; repeat a key for a list. |
| `api-key` | no |  | Your pdfrender API key, from a secret. Optional: without one the anonymous tier's lower limits apply; a key raises them. Create one at https://pdfrender.dev/go/gh-action?to=/app/api-keys |
| `output` | no |  | Where to write the answer (default: a file in the runner's temp directory, named after the operation). |
| `fail-if` | no |  | A jq expression on a JSON answer; the step fails when it is true (e.g. `.valid == false`). |
| `base-url` | no | `https://api.pdfrender.dev` | The API base URL. |

## Outputs

| Output | Description |
|---|---|
| `status` | The HTTP status of the last API call. |
| `output` | The path of the file holding the answer. |

## Operations

### `get_me`

Your plan, remaining requests, and remaining credits — `GET /v1/me`.

```yaml
- uses: pdfrender-dev/pdfrender-action@v1.0.0
  with:
    operation: get_me
    api-key: ${{ secrets.PDFRENDER_API_KEY }}
```

### `render_html_to_pdf`

Render an HTML document to a PDF — `POST /v1/render`.
Costs 1 credit.

```yaml
- uses: pdfrender-dev/pdfrender-action@v1.0.0
  with:
    operation: render_html_to_pdf
    json: |
      {
        "filename": "invoice.pdf",
        "html": "<!doctype html>\n<style>\n@page { size: A4; margin: 22mm }\nbody { font: 11pt/1.5 sans-serif; color: #1c2024 }\ntable { width: 100%; border-collapse: collapse }\nth { text-align: left; border-bottom: 2px solid #1c2024 }\ntd { padding: 3mm 0; border-bottom: 1px solid #e3e6ea }\n</style>\n<h1>Invoice 2026-041</h1>\n<p>Northwind Studio · Due Sep 30, 2026</p>\n<table>\n  <thead><tr><th>Description</th><th>Qty</th><th>Amount</th></tr></thead>\n  <tbody>\n    <tr><td>Design retainer, September</td><td>1</td><td>1,800.00</td></tr>\n    <tr><td>Illustration, cover set</td><td>6</td><td>720.00</td></tr>\n  </tbody>\n</table>\n"
      }
    api-key: ${{ secrets.PDFRENDER_API_KEY }}
```

API reference: https://pdfrender.dev/docs · Base URL: `https://api.pdfrender.dev`

## Support

https://pdfrender.dev/support

This repository is generated from the live API; changes to its files are overwritten.
