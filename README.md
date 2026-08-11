# Vale Resort & Hensol Castle Quoting Tools

A browser-based quoting suite for the Vale Resort and Hensol Castle sales and events team. It turns event details, guest numbers, tariffs and optional extras into consistent, itemised customer quotations without requiring a spreadsheet or a back-end service.

The repository started with the Hensol Castle wedding quoting engine and now also includes tools for private events, meetings and conferences, golf packages and sports teams, plus a booking letter tool that turns a confirmed golf booking into a customer confirmation letter, Pro Forma Invoice and email.

## Problem

Producing a venue quote involves more than multiplying a package price by the number of guests. A salesperson needs to account for the event date and season, adult and child guest counts, food and drink selections, accommodation, room hire, optional extras, discounts and VAT. Repeating those calculations manually is slow and makes it easy for quotes to be inconsistent.

This project provides a guided form for those inputs, applies the relevant pricing rules immediately and produces a clear line-item summary that can be printed or saved as a PDF for the customer.

## Dataset

There is no external CSV, database or API. The pricing dataset is encoded as JavaScript objects and constants inside each tool's `index.html` file.

For the wedding tool, the dataset includes:

- 2026-2028 venue hire tariffs, split by season, date range and day of the week
- adult, junior and under-eight wedding breakfast prices
- evening-food packages and dessert supplements
- wine, beer, soft-drink and non-alcoholic drink prices
- wedding extras such as canapes, arrival food, cheese tables and bridal breakfast
- pre-wedding party food packages

The other tools contain their own room, catering, accommodation, golf and event-specific pricing rules. Prices are treated as business tariffs, not as a training or statistical dataset. They should be reviewed whenever a new rate card is issued.

## Approach

The application uses plain HTML, CSS and JavaScript. Each quote type is a self-contained single-page tool:

1. The user enters the booking details and selects packages or extras.
2. JavaScript filters the available tariff data and applies per-person, per-item, per-room or package rules.
3. The quote recalculates in the browser and builds an itemised summary.
4. VAT is derived from VAT-inclusive totals using the current 20% rate encoded in the tools.
5. The completed quote is opened in a print-friendly window so the browser can print it or save it as a PDF.

This zero-build approach keeps deployment simple: the repository can be served by any static web server. It also means tariff data and calculation logic currently live together in the page source.

## Results

The repository provides a central launcher and five linked quoting workflows:

- Hensol Castle weddings
- private events
- meetings and conferences
- golf days and golf breaks
- sports teams

The generated quotes show customer and event details, selected items, quantities, unit prices, totals excluding VAT, VAT and the final VAT-inclusive total. The tools also support custom line items where a standard tariff does not cover the request, and the quote can be printed or saved as a PDF.

The result is a working internal sales aid rather than a measured research experiment. No accuracy benchmark, time-saving study or production usage metric is currently stored in this repository.

## Installation and use

No package manager, build step or application server is required.

### Option 1: open the files directly

```bash
git clone https://github.com/BethanLeeke/wedding-quote-tool.git
cd wedding-quote-tool
```

Open `index.html` in a modern browser and choose a quoting tool.

### Option 2: run a local static server

From the repository directory, run:

```bash
python -m http.server 8000
```

Then visit <http://localhost:8000>.

Allow pop-ups when generating a PDF quote: the tools open a print-friendly window and then use the browser's print dialog. An internet connection is also needed for externally hosted fonts and images.

## Project structure

```text
.
|-- index.html                       # tool launcher
|-- hensol-castle-weddings/          # wedding quoting engine
|-- private-events/                  # private event quotations
|-- meetings-conferences/            # meeting and conference quotations
|-- golf-packages/                   # golf day and golf break quotations
|-- sports-teams/                    # sports-team quotations
|-- vale-resort-dinner-dance/        # standalone dinner-dance tool
`-- Booking Letter Tool/             # post-confirmation booking letters
    `-- Golf Booking Letter Tool/    # golf day and golf break booking letters
```

## Booking Letter Tool

The quoting tools cover the pre-sale stage. The Booking Letter Tool covers what happens after the customer confirms.

`Booking Letter Tool/Golf Booking Letter Tool/` takes the Pro Forma Invoice produced by the property management system and turns it into a customer booking confirmation:

1. Choose **Golf Day** or **Golf Break**.
2. Drag and drop the Pro Forma Invoice PDF. It is read in the browser with [PDF.js](https://mozilla.github.io/pdf.js/); the booking ID, stay dates, group name, company, booking contact, email, phone, hotel contact and the bedroom lines (date, room type, quantity) are pulled through. Prices on the Pro Forma are deliberately ignored, and every parsed field stays editable.
3. Enter the package details, using the same fields as the golf quoting tool, plus the deposit required, the number of weeks it is due within and any deposit already paid.
4. Generate a print-ready booking letter containing the confirmation letter, the booking summary and bedrooms, a Pro Forma Invoice with bank details and signature block, and the Resort terms and conditions.
5. Copy the generated email template straight into Outlook.

Nothing is uploaded anywhere: the PDF is parsed locally in the browser.

## What I would improve next

1. Move tariffs into versioned JSON files so pricing can be reviewed and updated without editing calculation code.
2. Add automated tests for date bands, guest categories, discounts, room-night calculations, VAT and quote totals.
3. Consolidate repeated form, calculation and PDF code into shared modules to reduce drift between tools.
4. Add stronger validation and clear warnings for missing dates, unsupported tariff years and impossible guest combinations.
5. Improve accessibility and mobile usability, including keyboard navigation, form error messages and print testing.
6. Add controlled quote saving, reference numbers and an audit trail if the tool moves beyond local internal use.

## Important note

This is an internal quoting aid. Tariffs, VAT treatment, terms and generated totals should be checked against the latest approved commercial information before a quote is sent to a customer.
