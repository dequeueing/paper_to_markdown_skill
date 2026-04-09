# Paper to Markdown Skill

A Claude Code skill for converting academic papers (PDF) into well-structured Markdown files, with one file per section.

## Features

- Extract text from PDF using `pdftotext`
- Clean and normalize text (fix broken lines, format tables)
- Split into sections with proper Markdown formatting
- Organized output structure (`sections/` for final files, `logs/` for intermediate files)

## Installation

### Claude Code

```bash
# Clone this repository
git clone https://github.com/YOUR_USERNAME/paper-to-markdown.git
cd paper-to-markdown

# Install the skill (copy to Claude skills directory)
cp -r paper-to-markdown ~/.claude/skills/
```

### GitHub Copilot CLI

```bash
# Clone this repository
git clone https://github.com/YOUR_USERNAME/paper-to-markdown.git
cd paper-to-markdown

# Install the skill
cp paper-to-markdown.skill ~/.copilot/skills/
```

## Usage

### Claude Code

```
/paper-to-markdown
```

Then provide a task like:

```
Convert the PDF paper at /path/to/paper.pdf to markdown
```

### GitHub Copilot CLI

```
/paper-to-markdown
```

Then provide your request:

```
Convert /path/to/paper.pdf to markdown
```

The skill will:
1. Extract text using `pdftotext`
2. Clean and normalize the text
3. Split into sections under `sections/`
4. Save intermediate files under `logs/`

## Output Structure

```
<project-root>/
├── sections/           # Final Markdown artifacts
│   ├── 00_Abstract.md
│   ├── 01_Introduction.md
│   └── ...
├── logs/              # Intermediate files for auditing
│   ├── <paper-name>.txt           # Raw text extraction
│   └── <paper-name>_cleaned.txt    # Cleaned text
└── ...other project files...
```

## Workflow

1. **Check Available Tools**: Verify `pdftotext` is available
2. **Convert PDF to Text**: Extract text preserving layout
3. **Analyze Paper Structure**: Identify title, authors, abstract, sections
4. **Clean and Normalize Text**: Fix broken lines, format tables
5. **Split by Section**: Create one .md file per section
6. **Format as Markdown**: Apply proper heading hierarchy, tables, formulas

## Requirements

- `pdftotext` (poppler-utils) or Python with `pymupdf`

## License

MIT
