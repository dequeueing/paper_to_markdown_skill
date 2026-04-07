---
name: paper-to-markdown
description: >
  Convert academic papers (PDF) into well-structured Markdown files, with one file per section.
  Use this skill whenever the user wants to:
  - Convert a PDF paper to Markdown format
  - Split a paper's PDF into separate Markdown files by section
  - Extract and format a paper's content (abstract, introduction, references, appendices, tables, formulas)
  - Create a clean Markdown representation of an academic paper

  This skill handles the complete workflow: PDF text extraction, structure analysis, section splitting, and Markdown formatting.
  Works with arXiv papers, conference papers, journal articles, and other academic documents.
  **Do NOT use Read tool directly on PDF files** - always use pdftotext or similar tools first to extract text content.
---

# Paper to Markdown

Convert a PDF academic paper into well-structured Markdown files, one per section.

## Workflow

### Step 1: Check Available Tools

**Goal**: Confirm PDF text extraction tools are available.

```bash
# Check for pdftotext (poppler-utils)
which pdftotext

# Check alternatives
which pdf2txt.py  # Python pdfminer
which pdftoppm    # PDF to image
```

**If not available**, use Python:
```bash
pip install pymupdf  # or pypdf2, pdfplumber
```

### Step 2: Convert PDF to Text

**Command**:
```bash
pdftotext -layout /path/to/input.pdf logs/<paper-name>.txt
mkdir -p logs/
```

- `-layout`: Preserve original layout (tables, indentation)
- `-raw`: Cleaner plain text without layout
- `-enc UTF-8`: Specify encoding
- **Save to `logs/` directory** for auditing

**Python fallback**:
```python
import fitz  # pymupdf

doc = fitz.open("/path/to/input.pdf")
with open("/path/to/output.txt", "w", encoding="utf-8") as f:
    for page in doc:
        f.write(page.get_text())
```

**Verify success**:
```bash
wc -l output.txt  # Check line count
head -50 output.txt  # Confirm content is correct
```

### Step 3: Analyze Paper Structure

Read the full txt file and identify:

1. **Title** (usually within first 10 lines)
2. **Authors**
3. **Abstract** (usually after title, often marked "Abstract" or "ABSTRACT")
4. **Section titles** (numbered: 1, 2, 3... or uppercase headings like "INTRODUCTION", "RELATED WORK")
5. **References start** (marked "References", "Bibliography", "REFERENCES")

**Common section patterns**:
```
[Title/Authors]
[Abstract]

1. INTRODUCTION
2. RELATED WORK
3. PROBLEM FORMULATION
4. MODEL VERIFICATION TECHNIQUES
5. DISCUSSION
6. CONCLUSION
References
Appendix A
Appendix B
```

Note: Actual structure varies by paper - adapt the pattern matching accordingly.

### Step 4: Clean and Normalize Text

**IMPORTANT**: Raw text from `pdftotext` often has issues that must be fixed before splitting:

1. **Fix broken lines**: Lines may be cut mid-sentence. Join lines that end without proper punctuation (no `.`, `?`, `!`) and don't start with uppercase (likely continuation of previous line).

2. **Normalize whitespace**: Remove excessive blank lines, trim leading/trailing spaces.

3. **Fix table formatting**: Raw text tables often have misaligned columns. Reconstruct them using proper `|` pipe delimiters:
   - Identify column separators (look for aligned spaces or `|` characters)
   - Ensure header row has `|---|---|` separator line

4. **Fix formula display**: Formulas in raw text may be broken across lines. Join formula parts and wrap with `$$` delimiters.

5. **Fix lists**: Numbered/bulleted lists may lose their structure. Re-add proper `-` or `1.` prefixes.

**Save cleaned text**: After cleaning, save to `logs/<paper-name>_cleaned.txt` for reference.

**Example cleanup logic**:
```
# If a line doesn't end with proper punctuation AND next line doesn't start uppercase
# → join them into one paragraph

# If lines have similar indentation pattern and repeated separators
# → convert to markdown table format
```

### Step 5: Split by Section

Read the cleaned txt file, split content by section headings, and write each section to a separate `.md` file.

**Naming convention**: Use two-digit prefixes for ordering:
- `sections/00_Abstract.md`
- `sections/01_Introduction.md`
- `sections/02_Related_Work.md`
- etc.

**Output structure**:
```
<project-root>/
├── sections/           # Final Markdown artifacts (one per section)
│   ├── 00_Abstract.md
│   ├── 01_Introduction.md
│   └── ...
├── logs/              # Intermediate files for auditing/debugging
│   ├── <paper-name>.txt           # Raw text extraction
│   └── <paper-name>_cleaned.txt    # Cleaned text (if applicable)
└── ...other project files...
```

### Step 6: Format as Markdown

Apply proper Markdown formatting:

**Headings**:
- `#` for main title
- `##` for section titles
- `###` for subsections

**Tables** (convert to pipe format):
```
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Data     | Data     | Data     |
```

**Formulas**:
- Inline: `$E = mc^2$`
- Block:
```
$$
MMD^2(P, Q) = E_{x,x'\sim P}[k(x, x')] + \cdots
$$
```

**Code blocks**:
````
```python
def example():
    pass
```
````

**Lists**: Use `-` or `1.` for ordered/unordered

## Common Errors

| Error | Problem | Correct Approach |
|-------|---------|-----------------|
| Use Read directly on PDF | PDF renders as image, no extractable text | Use `pdftotext` first |
| Skip text cleanup | Lines broken, tables messy, unreadable output | Always clean text before splitting |
| Ignore table formatting | Tables become plain text | Convert to `|` format |
| Wrong split point | References mixed into previous section | Find precise "References" marker |

## Output Validation

After creating Markdown files:
```bash
ls -la sections/       # List all output Markdown files
ls -la logs/          # List intermediate files
head -20 sections/00_Abstract.md  # Check first section file
```