# How to Upload This Project to GitHub — Step by Step

Two routes are covered: **(A) the website** (easiest, no install) and **(B) Git on the command line** (the professional way). Pick one.

---

## What you'll upload

Put these files in one project folder before you start:

```
hiv-treatment-effectiveness-analysis/
├── README.md                          ← repo front page (included)
├── data/
│   ├── HIV_dataset.xlsx               ← raw WHO data
│   └── HIV_Star_Schema_Model.xlsx     ← cleaned star schema model
├── dashboard/
│   └── HIV_Dashboard.pbix             ← your Power BI file
├── report/
│   ├── HIV_Analysis_Report.pdf        ← detailed report
│   └── HIV_Presentation.pdf           ← the storytelling deck
├── docs/
│   ├── PowerBI_Step_by_Step_Guide.md
│   └── GitHub_Upload_Guide.md
└── images/                            ← exported chart screenshots (optional)
```

> ⚠️ GitHub's web upload limit is **25 MB per file** (100 MB hard limit via Git). A `.pbix` can be large — if yours exceeds 25 MB, use route B or **Git LFS** (see the note at the end).

---

## Route A — Upload through the GitHub website (no install)

1. **Create an account** at [github.com](https://github.com) and sign in.
2. Click the **+** (top-right) → **New repository**.
3. Fill in:
   - **Repository name:** `hiv-treatment-effectiveness-analysis`
   - **Description:** "Global HIV trends & treatment effectiveness analysis (Power BI) — WHO data, 2000–2024."
   - **Public** (so others, and 10Alytics, can see it).
   - ✅ Check **Add a README file**.
4. Click **Create repository**.
5. On the repo page, click **Add file → Upload files**.
6. **Drag your whole project folder** (or the files) into the box. To create folders like `data/`, type the path into the filename, e.g. upload then name it `data/HIV_dataset.xlsx` — or just drag the folder and GitHub keeps the structure.
7. Scroll down to **Commit changes**, write a message like `Add project files, report and dashboard`, and click **Commit changes**.
8. Replace the starter README: click `README.md` → ✏️ (Edit) → paste the contents of the included `README.md` → **Commit changes**.

Done. Your repo is live at `https://github.com/<your-username>/hiv-treatment-effectiveness-analysis`.

---

## Route B — Git on the command line (professional)

### One-time setup
1. **Install Git** from [git-scm.com](https://git-scm.com/downloads).
2. Tell Git who you are:
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```

### Create the remote repo
3. On github.com, create a **New repository** (same name as above) but this time **do NOT** check "Add a README" — keep it empty.

### Push your local folder
4. Open a terminal **inside your project folder** and run:
   ```bash
   git init
   git add .
   git commit -m "Initial commit: HIV analysis project, report, deck and dashboard"
   git branch -M main
   git remote add origin https://github.com/<your-username>/hiv-treatment-effectiveness-analysis.git
   git push -u origin main
   ```
5. If prompted to authenticate, use a **Personal Access Token** (GitHub → Settings → Developer settings → Personal access tokens) as the password.

### Future updates
```bash
git add .
git commit -m "Describe what you changed"
git push
```

---

## Recommended `.gitignore`

Create a file named `.gitignore` in the root to keep junk out of the repo:

```gitignore
# OS / editor noise
.DS_Store
Thumbs.db
*.tmp
~$*                 # Office lock files (e.g. ~$report.docx)

# Power BI cache (optional)
*.pbix.bak

# Large/derived files you may not want tracked
# *.pbix            # uncomment if using Git LFS instead
```

---

## If your `.pbix` is larger than 25 MB — use Git LFS

```bash
git lfs install
git lfs track "*.pbix"
git add .gitattributes
git add dashboard/HIV_Dashboard.pbix
git commit -m "Track .pbix with Git LFS"
git push
```

---

## Final checklist before you submit

- [ ] Repo is **Public**.
- [ ] `README.md` renders correctly on the front page (headings, table, links).
- [ ] `data/`, `dashboard/`, `report/`, `docs/` folders are present.
- [ ] The PDF report and presentation open correctly from GitHub.
- [ ] Add the repo link to your LinkedIn post and **tag 10Alytics**.
