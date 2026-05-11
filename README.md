# DataMerge Studio

A web application for combining CSV and Excel files using **all standard SQL set
operations** — Union All, Union All (Distinct), and Inner / Left / Right / Full Outer /
Cross Join — with smart column mapping, data-type cleaning, and an interactive
dashboard. Works with any files, any column structure.

---

## Who Is This For?

Anyone who works with multiple spreadsheets and needs to combine them — from monthly
exports that should be stacked, to two systems' data joined on a shared ID column.
No SQL knowledge required: the app explains every operation visually with sample data
before you run it.

### Local vs Cloud

A hosted demo is available at **https://filemerger-kb.streamlit.app**, but not all
features work there because the cloud server cannot access your PC's file system.

| Feature | Cloud demo | Local (`run_app.bat`) |
|---------|-----------|----------------------|
| Learn Merge & Joins | ✅ | ✅ |
| Upload + Merge + Download | ✅ | ✅ |
| Dashboard | ✅ | ✅ |
| **Folder Mode** | ❌ needs local files | ✅ |

**For Folder Mode, always run the app locally via `run_app.bat`.**

---

## Quick Start (Windows)

1. Make sure **Python 3.9+** is installed. Download from https://www.python.org/downloads/
   During install, check ✅ "Add Python to PATH".

2. Double-click **`run_app.bat`** — it installs all required packages and launches the app.

3. Your browser opens at **http://localhost:8501**

To stop the app, press `Ctrl+C` in the terminal window.

---

## Manual Setup (if run_app.bat does not work)

```
pip install -r requirements.txt
streamlit run file_merger_app.py
```

---

## Supported File Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| Excel (modern) | `.xlsx` | Recommended |
| Excel (legacy) | `.xls` | Fully supported |
| CSV | `.csv` | Any delimiter; auto-detected |

Any number of columns, any column names — the app adapts.

---

## The 7 Operations

DataMerge Studio implements all standard SQL set operations:

### 🔵 Union family — vertical stacking

**1. Union All** — Stack all rows from all files. Every row kept, duplicates included.
*Use when:* Combining periodic exports (daily/monthly files) with no overlap.

**2. Union All (Distinct)** — Stack vertically, then drop rows identical in every column.
*Use when:* Two exports of the same data with minor irrelevant differences.

### 🔗 Join family — horizontal combination on a key column

**3. Inner Join** — Only rows whose key exists in EVERY file; columns combined.
*Use when:* Records confirmed across every source.

**4. Left Join** — Keep ALL rows from File 1; add File 2 columns where key matches.
*Use when:* Enriching a master list with extra info from another file.

**5. Right Join** — Keep ALL rows from the LAST file; mirror of Left Join.
*Use when:* The latest file is the source of truth.

**6. Full Outer Join** — Keep EVERY row from EVERY file; combine matched rows.
*Use when:* Building a complete master list from disparate sources.

**7. Cross Join** — Cartesian product (every row × every row).
*Use when:* Generating all combinations. Excel row limit pre-checked.

---

## Application Tabs

### 1. 📚 Learn Merge & Joins
Live interactive examples using dummy data. Each operation has a Venn diagram and a
before/after table showing exactly what it produces.

### 2. 📁 Upload Files
Drag and drop 2–20 files. Preview the first rows of each sheet and see row/column counts.
**"New Session" button** at top right with confirmation — clears all current work.

### 3. 🔀 Merge & Download
Three-step process:
- **Step 1 — Column Alignment:** if files have different column names, a mapping form
  appears with **10-row sample previews of every column** so you can verify two
  similarly-named columns (e.g. "Sr No" vs "Ser Num") actually contain the same kind of data.
- **Step 2 — Merge Settings:** pick the operation; a **5-row output preview** shows
  exactly what the merge will produce on your real data before you commit.
- **Step 3 — Sheet Groups:** all sheets participate in one combined operation
  (chained join, or stacked union).

After every merge you also see:
- **Data Type Cleaning report** listing every date parsed, whitespace stripped, etc.
- **Duplicate Audit** panel for Union All (Distinct) — exact row numbers removed, downloadable.
- **Zero-row warning** for joins where the key doesn't match anywhere.
- **All results persist** until you start a New Session — survive every widget click.

### 4. 📂 Folder Mode
⚠️ **Requires the app to be running locally** (`run_app.bat`).
Point to a folder on your computer. Auto-detects CSV/Excel files. Supports
**incremental append** — if you ran a merge before, only new files are added.

### 5. 📊 Dashboard
Auto-generates charts from the merged data:
- Records by source file, date trends, categorical breakdowns
- Numeric summary, missing value report, filterable data explorer
- **Export as standalone HTML** that works offline

If your data has no dates, no numeric columns, and no categorical columns suitable for
charting, the dashboard says so clearly rather than showing a blank screen.

---

## Column Alignment

When you upload files where the same data has different column names
(e.g. "SR No" in one, "Ticket ID" in another), the app:

1. **Shows a 10-row sample of every column** from every file so you can see what's in them.
2. **Detects mismatches** and shows a mapping form.
3. Type the same canonical name for both → they're renamed and treated as one column.

Sample preview is the key UX feature — you don't need to open the source files to verify
that "Serial Num" really is the same as "SR No" (it might be the instrument serial, not the ticket ID).

---

## Data Type Cleaning

Enabled by default in Merge Settings. Applied automatically:

| Column type | What happens |
|-------------|-------------|
| Column name contains: `date`, `time`, `created`, `due`, `closed`, `updated`, etc. | Parsed to datetime (dayfirst) — only if ≥50% parse successfully |
| Column name contains: `city`, `town`, `region`, `zone`, `area`, `state`, `country` | Whitespace stripped + title-cased |
| Any other string column | Whitespace stripped |

Output dates are dashboard- and Power-BI-friendly.

---

## Files in This Package

| File | Purpose | In git? |
|------|---------|---------|
| `file_merger_app.py` | Main Streamlit web application | ✅ Yes |
| `requirements.txt` | Python package dependencies | ✅ Yes |
| `run_app.bat` | Windows launcher — double-click to start | ✅ Yes |
| `README.md` | This file | ✅ Yes |
| `merge_files.py` | Personal script for Pending Calls files | ❌ Gitignored |
| `save_to_sql.py` | Personal SQL Server push script | ❌ Gitignored |
| `Raw Files/` | Source Excel files | ❌ Gitignored |
| `MERGED_*.xlsx` | Generated output files | ❌ Gitignored |
| `__pycache__/` | Python bytecode cache | ❌ Gitignored |

---

## Requirements

```
pandas >= 2.0
openpyxl >= 3.1
xlrd >= 2.0
streamlit >= 1.32
matplotlib >= 3.7
matplotlib-venn >= 0.11
plotly >= 5.0
```
