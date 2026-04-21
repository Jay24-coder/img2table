# img2table

Small workspace for extracting tables from PDFs with the [img2table](https://pypi.org/project/img2table/) library and [Tesseract](https://github.com/tesseract-ocr/tesseract) OCR, using the walkthrough in `img2table.ipynb`.

## About the img2table library

[img2table](https://github.com/xavctn/img2table) identifies and extracts tables from **images** and **PDFs** using **OpenCV**-based image processing. It is meant as a lighter, CPU-friendly option compared with many neural-network-heavy table pipelines.

**What it does**

- Finds tables and exposes **cell-level bounding boxes** (not only the outer table box).
- Handles **merged cells** and other non-trivial layouts.
- Can infer **implicit rows and columns** (optional flags on `extract_tables`; see the upstream [Implicit](https://github.com/xavctn/img2table/blob/main/examples/Implicit.ipynb) example).
- Optionally extracts **borderless** tables when combined with OCR (with documented limitations; borderless mode is stricter for small tables).
- Returns each table as an **`ExtractedTable`** with **`df`** (pandas), **`html`**, **`content`** (cells with values and boxes), **`bbox`**, and **`title`**.
- Can **export to Excel** (`.xlsx`) with **`to_xlsx`**, one worksheet per table, preserving structure where possible.

**Inputs**

- **Images:** common formats supported via OpenCV (e.g. PNG, JPEG, TIFF, WebP, BMP); multi-page image files are not supported.
- **PDFs:** **native** and **scanned** PDFs; pages are rendered at **200 DPI** for detection. For native PDFs, text can be read from the file when **`pdf_text_extraction=True`** so OCR is not always required for text.

**Documents API (high level)**

- **`Image`** / **`PDF`** accept paths, `pathlib.Path`, `bytes`, or buffers; **`PDF`** supports **`pages=`** (0-based indexes), **`detect_rotation`** (skew/rotation up to about **45°**), and **`pdf_text_extraction`**.
- **`extract_tables`** takes **`ocr`**, **`implicit_rows`**, **`implicit_columns`**, **`borderless_tables`**, and **`min_confidence`** (0–99, OCR quality gate).

**OCR backends**

The base **`pip install img2table`** install is oriented around **Tesseract** (system install required). Optional extras add other engines, for example **`img2table[paddle]`**, **`[easyocr]`**, **`[surya]`**, **`[gcp]`**, **`[aws]`**, **`[azure]`**; **docTR** is documented as a separate user install. Full constructor options and authentication for cloud OCRs are in the [upstream README](https://github.com/xavctn/img2table/blob/main/README.md).

**Caveats (from upstream)**

Extraction quality depends strongly on **OCR quality**; tables with no usable OCR output may be skipped. The pipeline is aimed at **light backgrounds**; difficult scans or detection limits may need different tools.

## Prerequisites

- **Python** 3.13 or newer (see `pyproject.toml`).
- **Tesseract** installed on the system and available on `PATH`, with the language data your notebook uses (the notebook sets `lang="eng"` on `TesseractOCR`).
- **Input PDF** named `sample-tables.pdf` in the working directory when you run the notebook (the notebook opens that path; the file is not shipped in this repository).

## Install

From the repository root, using [uv](https://docs.astral.sh/uv/):

```bash
uv sync --group notebook
```

That installs the project dependencies (`img2table`, `pillow`, and their transitive packages) plus **IPython** and **ipykernel** (for `IPython.display.display_html` in the notebook). To install only the library set without Jupyter tooling, use `uv sync`.

## Notebook workflow

`img2table.ipynb` does the following, in order:

1. Import `PDF` from `img2table.document`, `TesseractOCR` from `img2table.ocr`, and `display_html` from `IPython.display`.
2. Load `sample-tables.pdf`, configure `TesseractOCR(n_threads=1, lang="eng")`, and call `doc.extract_tables(...)` with `implicit_rows`, `implicit_columns`, `borderless_tables`, and `min_confidence` set as in the notebook.
3. Inspect the `extracted_tables` mapping (page index → list of `ExtractedTable` objects).
4. Loop over pages and tables and render each table with `display_html(table.html_repr(title=...), raw=True)`.
