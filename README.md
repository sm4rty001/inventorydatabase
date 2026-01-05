# Inventory Database 

A small static web app for browsing and exploring a collection of retro computing items. The site renders an inventory table and provides client side search, platform/location filters, value formatting and an image search drawer for item art.

---

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Data Model](#data-model)
- [Control Flow](#control-flow)
- [How to Run](#how-to-run)
- [Regenerating the HTML from the spreadsheet](#regenerating-the-html-from-the-spreadsheet)
- [Potential Improvements](#potential-improvements)
- [Personal Development](#personal-development)

---

## Overview

This repository contains a lightweight Inventory Viewer implemented as static HTML, CSS and JavaScript. The inventory data is provided as a spreadsheet (`inventory.xlsx`) which is converted to an HTML table that can be viewed locally via `index.html` or `inventory.html`.

The UI supports typing a search term, filtering by platform and location and clicking rows to open an inline image search panel (supports Google/Bing/Wikipedia/Unsplash when API keys are available).

---

## Features 

- Fast client side search and filter by platform and location
- Automatic currency formatting for the **Value** column (displays as `£`) 
- Clicking a row opens an image search drawer for item artwork
- Accessible controls (ARIA labels) and responsive layout
- Simple single file deployment (drop into any static host)
- Example generator scripts: **Python** (`tools/generate_table.py`) and **PowerShell** (`tools/generate_table.ps1`) to convert `inventory.xlsx` into an HTML table stored externally.

---

## Project Structure 

- `index.html` — main interactive viewer (search, filters, image drawer)
- `inventory.html` — static table only page (exported HTML)
- `template.html` — simple template used to inject the generated table
- `inventory.xlsx` — canonical data source (spreadsheet)
- `styles.css` — styling for the viewer
- `README.md` — this file

---

## Data Model

Data is stored in `inventory.xlsx`. The spreadsheet is converted to an HTML `<table>` (see `template.html`) and the page JavaScript enhances the DOM to provide filtering, formatting and image searching. No server-side runtime is required to use the viewer.

Column names expected (case insensitive): `Platform`, `Title`, `Manufacturer`, `Format`, `CaseType`, `Value`, `Location`.

---

## Control Flow

- On page load the script finds the table and builds an in memory list of rows
- Filters (`search`, `platform`, `location`) are populated from table values
- Typing or changing filters runs a debounced `applyFilters()` that shows/hides rows
- Clicking a row composes a search query (Title + Platform + Manufacturer) and opens the image drawer

---

## How to Run 

Open `index.html` in any modern browser (double-click or use `Open File`). For a local web server (recommended to avoid CORS issues while developing):

```bash
# from this repo folder
python -m http.server 8000
# then visit http://localhost:8000/
```

No build step or external dependencies are required to view the inventory.

---

## Regenerating the HTML from the spreadsheet 

The repo keeps `inventory.xlsx` as the source of truth. To recreate `inventory.html` (or inject the table into `template.html`) you can use a short Python script with pandas. Example:

```python
# tools/generate_table.py
import pandas as pd
from pathlib import Path

df = pd.read_excel('inventory.xlsx')
html_table = df.to_html(index=False, escape=False)
template = Path('template.html').read_text()
Path('inventory.html').write_text(template.replace('{{TABLE_DATA}}', html_table))
```

Run:

```bash
python tools/generate_table.py
```

After regenerating, open `index.html` or `inventory.html` to verify formatting and filters.

---

## Potential Improvements 

- Add edit/create/delete UI and persist changes back to a CSV/XLSX or a lightweight server
- Add pagination or virtual scrolling for very large inventories
- Add tests and CI to validate table generation from the spreadsheet
- Improve the image search fallback logic and allow manual image assignment
- Add an export feature (CSV/JSON)

---

## Personal Development

Working on this project has helped me gain practical experience with repository setup and contribution workflows — I helped the owner set the repository up under their account and have continued to make improvements and submit pull requests when changes were needed.

It also significantly deepened my understanding of scripting and automation. I created generator scripts (Python and PowerShell) to convert the file `inventory.xlsx` into HTML and integrated an hourly update process so the inventory table stays current. Working with this pipeline taught me practical approaches for scraping and cleaning data and gave me familiarity with multiple routes I might take if I reimplemented or extended the project for example, exporting from the source system, writing robust scrapers with error handling, or building a small API to serve the inventory directly.


