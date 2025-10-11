# Common Commands Reference

**Quick Guide:** Copy any command below, paste into Terminal, and press Enter!

**Last Updated:** 2025-10-11 (Updated after folder reorganization)

---

## 📂 Folder Structure

Your files are now organized:
```
MyOwn_Research/
├── OrderRejectionProject/       ← All project documents here
│   ├── Order_Rejection_System_Design.md
│   ├── Order_Rejection_LLM_Knowledge_Base.md
│   ├── Retailer_Order_Rejection_Reasons.md
│   └── PDFs/                    ← Generated PDFs go here
├── Research/                    ← Source research materials
│   └── ResearchNotes.md
└── (other files)
```

---

## 📄 Convert Markdown to PDF (Pandoc)

### Convert System Design Document

```bash
pandoc OrderRejectionProject/Order_Rejection_System_Design.md -o OrderRejectionProject/PDFs/Order_Rejection_System_Design.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**What this does:** Converts the system design markdown file to a professional PDF with table of contents and numbered sections.

**Output location:** `OrderRejectionProject/PDFs/Order_Rejection_System_Design.pdf`

---

### Convert Knowledge Base Document

```bash
pandoc OrderRejectionProject/Order_Rejection_LLM_Knowledge_Base.md -o OrderRejectionProject/PDFs/Order_Rejection_LLM_Knowledge_Base.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**What this does:** Converts the knowledge base markdown file to a professional PDF with table of contents and numbered sections.

**Output location:** `OrderRejectionProject/PDFs/Order_Rejection_LLM_Knowledge_Base.pdf`

---

### Convert Retailer Rejection Reasons Document

```bash
pandoc OrderRejectionProject/Retailer_Order_Rejection_Reasons.md -o OrderRejectionProject/PDFs/Retailer_Order_Rejection_Reasons.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**What this does:** Converts the retailer rejection analysis markdown file to a professional PDF.

**Output location:** `OrderRejectionProject/PDFs/Retailer_Order_Rejection_Reasons.pdf`

---

### Convert ALL Order Rejection Documents at Once

```bash
pandoc OrderRejectionProject/Order_Rejection_System_Design.md -o OrderRejectionProject/PDFs/Order_Rejection_System_Design.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in && pandoc OrderRejectionProject/Order_Rejection_LLM_Knowledge_Base.md -o OrderRejectionProject/PDFs/Order_Rejection_LLM_Knowledge_Base.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in && pandoc OrderRejectionProject/Retailer_Order_Rejection_Reasons.md -o OrderRejectionProject/PDFs/Retailer_Order_Rejection_Reasons.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**What this does:** Converts all three documents to PDF in one command! Saves time.

**Output location:** All PDFs created in `OrderRejectionProject/PDFs/` folder

---

### Convert ANY Markdown File (Template)

```bash
pandoc FILENAME.md -o FILENAME.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**How to use:** Replace `FILENAME` with your actual file name (without spaces, or use quotes around it).

**Example:**
```bash
pandoc "My Document.md" -o "My Document.pdf" --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

---

## 📝 Pandoc Command Explained

Here's what each part means:

- `pandoc` - The conversion tool
- `input.md` - Your source markdown file
- `-o output.pdf` - Name of the PDF to create
- `--pdf-engine=xelatex` - Use XeLaTeX engine (supports Unicode, Hindi, special characters)
- `--toc` - Add Table of Contents
- `--number-sections` - Number chapters/sections automatically
- `-V geometry:margin=1in` - Set 1-inch margins on all sides

---

## 🔧 Pandoc Options (Mix and Match)

### Simple conversion (no extras)
```bash
pandoc input.md -o output.pdf
```

### With table of contents only
```bash
pandoc input.md -o output.pdf --toc
```

### With numbered sections only
```bash
pandoc input.md -o output.pdf --number-sections
```

### Custom margins
```bash
pandoc input.md -o output.pdf -V geometry:margin=0.5in
```

### All options combined (recommended)
```bash
pandoc input.md -o output.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

---

## 🐍 Python Scripts

### Generate Research Notes

```bash
python3 generate_research_notes.py
```

**What this does:** Scans all files in Research/ folder and creates/updates `Research/ResearchNotes.md` with summaries.

**Output location:** `Research/ResearchNotes.md`

**When to use:** After adding new PDF or document files to the Research folder.

---

## 📦 Git Commands (Version Control)

### Check Status (See what changed)

```bash
git status
```

**What this does:** Shows which files have been added, modified, or deleted.

---

### Add All Changes

```bash
git add .
```

**What this does:** Stages all your changes to be committed (ready to save).

---

### Add Specific File

```bash
git add FILENAME.md
```

**Example:**
```bash
git add COMMANDS.md
```

---

### Commit Changes (Save with message)

```bash
git commit -m "Brief description of what you changed"
```

**Examples:**
```bash
git commit -m "Add order rejection playbook documents"
git commit -m "Update research notes"
git commit -m "Convert documents to PDF"
```

---

### Push to GitHub (Upload changes)

```bash
git push
```

**What this does:** Uploads your committed changes to GitHub.

---

### Complete Git Workflow (All steps together)

```bash
git status
git add .
git commit -m "Your message here"
git push
```

**Use this:** When you want to save and upload all your changes in one go.

---

## 🗂️ File Operations

### Navigate to Project Directory

```bash
cd /Users/sorabhvijvij/GitProjects/MyOwn_Research
```

**What this does:** Changes to your project folder.

---

### List Files in Current Directory

```bash
ls -lh
```

**What this does:** Shows all files with sizes in human-readable format (KB, MB).

---

### List Only PDF Files

```bash
ls -lh *.pdf
```

---

### List Only Markdown Files

```bash
ls -lh *.md
```

---

### Open File in Default App

```bash
open FILENAME.pdf
```

**Example:**
```bash
open OrderRejectionProject/PDFs/Order_Rejection_System_Design.pdf
```

**Tip:** Use Tab autocomplete - type `open Order` then press Tab!

---

### Open Current Folder in Finder

```bash
open .
```

---

## 🔍 Search Commands

### Find Files by Name

```bash
find . -name "*.md"
```

**What this does:** Finds all markdown files in current directory and subdirectories.

---

### Search Inside Files

```bash
grep -r "search term" .
```

**What this does:** Searches for text inside all files in current directory.

---

## 🧹 Cleanup Commands

### Delete All PDF Files (USE CAREFULLY!)

```bash
rm *.pdf
```

**Warning:** This permanently deletes files. Make sure you have backups!

---

### Delete Specific File

```bash
rm FILENAME.pdf
```

---

## 📚 Getting Help

### Pandoc Help

```bash
pandoc --help
```

### Git Help

```bash
git --help
```

### Check Pandoc Version

```bash
pandoc --version
```

### Check Git Version

```bash
git --version
```

---

## 🚀 Quick Reference: Most Used Commands

**1. Convert markdown to PDF:**
```bash
pandoc FILENAME.md -o FILENAME.pdf --pdf-engine=xelatex --toc --number-sections -V geometry:margin=1in
```

**2. Save changes to Git:**
```bash
git add .
git commit -m "Your message"
git push
```

**3. Update research notes:**
```bash
python3 generate_research_notes.py
```

**4. Check what changed:**
```bash
git status
```

**5. Navigate to project:**
```bash
cd /Users/sorabhvijvij/GitProjects/MyOwn_Research
```

---

## 💡 Tips for Beginners

1. **Always run commands from your project directory**
   - First: `cd /Users/sorabhvijvij/GitProjects/MyOwn_Research`
   - Then: Run your command

2. **Copy commands exactly as written**
   - Don't change spacing or dashes
   - Replace only the parts in CAPITALS (like FILENAME)

3. **Check for errors**
   - If command fails, read the error message
   - Usually tells you what's wrong

4. **Use Tab to autocomplete filenames**
   - Type first few letters
   - Press Tab key
   - Terminal completes the filename

5. **Use Up Arrow to repeat last command**
   - Press Up Arrow key
   - Terminal shows your previous command
   - Press Enter to run again

---

## 🛠️ Installation Check (One-time setup)

### Check if Pandoc is Installed

```bash
which pandoc
```

**Expected result:** Should show a path like `/opt/homebrew/bin/pandoc`

If nothing shows, install with:
```bash
brew install pandoc
```

---

### Check if LaTeX is Installed

```bash
which xelatex
```

**Expected result:** Should show a path like `/Library/TeX/texbin/xelatex`

If nothing shows, install with:
```bash
brew install basictex
```

Then add to PATH:
```bash
export PATH=/Library/TeX/texbin:$PATH
```

---

## 📞 Need More Help?

- **Pandoc documentation:** https://pandoc.org/MANUAL.html
- **Git basics:** https://git-scm.com/docs
- **Homebrew (for installations):** https://brew.sh/

---

## 📝 Notes Section (Add Your Own Commands Below)

<!-- Add your frequently used commands here -->

```bash
# Example: Your custom commands go here

```

---

**End of Commands Reference**
