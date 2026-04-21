# img2table-python

Small workspace for extracting tables from PDFs with the [img2table](https://pypi.org/project/img2table/) library and [Tesseract](https://github.com/tesseract-ocr/tesseract) OCR, using the walkthrough in `img2table.ipynb`.

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

Upstream API details and other OCR backends are documented on the [img2table](https://pypi.org/project/img2table/) project page.
