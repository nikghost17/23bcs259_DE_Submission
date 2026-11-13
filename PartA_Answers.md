# Part A — Git and CI/CD
### Assignment: From Git to Gold | Roll No: 23BCS259

---

## A1. Repository Structure

GitHub repository: **https://github.com/nikghost17/23bcs259_DE_Submission**

```
freshcart-pipelines/
├── README.md
├── .gitignore              ← ignores .env, *.pkl, *.csv, mage.log, __pycache__/
├── requirements.txt
├── elt/                    ← FreshCart ELT notebook (Part B)
├── iris_project/           ← Mage ML project (Part C)
├── stream/                 ← dispatcher.py and driver.py (Part D)
├── tests/
│   └── test_prepare_data.py
└── .github/workflows/ci.yml
```

**`git log --oneline` output:**
```
7289710 feat: initialise repo structure with folders, .gitignore, requirements and CI workflow
```
*(Additional commits will appear here as each part is completed)*

---

## A2. Where Does Each Command Act?

| Command | Where the change happens | What exactly happens? |
|---|---|---|
| `git init` | Local machine — **Working folder** | Creates a hidden `.git/` directory inside the current folder; this directory becomes the local repository that Git uses to track all future changes and history. |
| `git add train.py` | Local machine — **Staging area (index)** | Copies the current version of `train.py` from the working folder into the staging area, marking it as ready to be included in the next commit snapshot. |
| `git commit -m "msg"` | Local machine — **Local repository** | Permanently saves a snapshot of everything currently in the staging area into the local `.git/` repository as a new commit, labelled with the given message. |
| `git remote add origin URL` | Local machine — **Local repository config** | Stores the remote GitHub URL under the alias `origin` inside the local Git config file; no files are transferred yet — it only registers where to push or pull from. |
| `git push -u origin main` | **Remote — GitHub repository** | Uploads all local commits on the `main` branch to GitHub and links the local `main` to `origin/main` so future `git push` and `git pull` commands need no extra arguments. |
| `git pull origin main` | Local machine — **Working folder + Local repository** | Fetches the latest commits from GitHub's `main` branch and merges them into the local repository and working folder, keeping the local copy in sync with the remote. |

### Question: After `git commit`, can your teammate see your code on GitHub? Why or why not?

**No.** `git commit` only saves the snapshot to the **local repository** on your own machine — GitHub is not involved at this step at all. The teammate can only see the code after `git push` is run, which is the command that actually uploads the local commits to the remote GitHub repository.

---

## A3. CI Workflow

### Test file — `tests/test_prepare_data.py`

```python
from sklearn.datasets import load_iris
from iris_project.transformers.prepare_data import transform


def test_split_is_80_20():
    """Verifies that prepare_data splits the Iris dataset 80/20 (train/test)."""
    df = load_iris(as_frame=True).frame
    out = transform(df)
    assert (out["split"] == "train").sum() == 120
    assert (out["split"] == "test").sum() == 30
```

### Workflow file — `.github/workflows/ci.yml`

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python 3.12
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest tests/ -v
```

### Screenshots

**Green run (passing):**

> *(Screenshot to be added after Part C — `prepare_data.py` is created)*

**Red run (failing — `test_size` temporarily changed to `0.3`):**

> *(Screenshot to be added)*

**Fix commit (restoring `test_size` to `0.2`):**

> *(Screenshot to be added)*
