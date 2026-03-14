# Data Versioning using DVC ✅

Data versioning tools are used in MLOps to track, manage, and reproduce different versions of datasets used in machine learning projects.
Just like code versioning in Git, data versioning tools help track changes in datasets over time.

# Why Data Versioning is Important

## In ML projects:

* Data keeps changing

* Models depend on specific dataset versions

* Experiments need reproducibility

## Data versioning helps to:

* Track dataset changes

* Reproduce experiments

* Roll back to older datasets

* Collaborate with teams





# DVC + Git Versioning Workflow

This guide explains how to use **Git and DVC (Data Version Control)** together to version control your **data files and machine learning datasets**.

Normally, **Git is not designed to track large datasets**.
DVC solves this problem by tracking **data separately while Git tracks the metadata**.

---

# Project Workflow

## 1. Create a Git Repository and Clone it

First create a repository on GitHub and clone it to your local machine.

```bash
git clone <your-repo-url>
cd <repo-name>
```

This will create a **local working copy** of your repository.

---

# 2. Create `mycode.py`

Create a Python file that generates or saves data.

Example:

```python
import pandas as pd
import os

data = {
    "Name": ["A", "B", "C"],
    "Score": [80, 90, 85]
}

df = pd.DataFrame(data)

os.makedirs("data", exist_ok=True)
df.to_csv("data/data.csv", index=False)

print("CSV file created successfully")
```

This script:

* Creates a **dataset**
* Saves it inside a new folder called **data/**
* Generates a CSV file

---

# 3. Commit Code Before Initializing DVC

Before using DVC, commit your current project state.

```bash
git add .
git commit -m "Initial project setup"
git push
```

Now create a **virtual environment** and install DVC.

```bash
python -m venv dvc
```

Activate the environment.

Windows:

```bash
.\dvc\Scripts\activate
```

Install DVC:

```bash
pip install dvc
```

This may take **2–3 minutes** because DVC installs several dependencies.

---

# 4. Initialize DVC

Now initialize DVC in the repository.

```bash
dvc init
```

This command creates:

* `.dvc/` → DVC internal metadata
* `.dvcignore` → files ignored by DVC

Commit these files.

```bash
git add .
git commit -m "Initialize DVC"
```

---

# 5. Create Storage Directory (Example: S3)

Create a folder that will act as a **remote storage location**.

```bash
mkdir S3
```

In real production environments this could be:

* AWS S3
* Google Cloud Storage
* Azure Blob Storage
* SSH server

---

# 6. Add Remote Storage

Connect DVC to the storage location.

```bash
dvc remote add -d myremote S3
```

Explanation:

* `remote add` → adds remote storage
* `-d` → sets it as default
* `myremote` → name of remote
* `S3` → path/URL of storage

Commit this change.

```bash
git add .
git commit -m "Add DVC remote storage"
```

---

# 7. Add Data Folder to DVC

Track the **data folder** with DVC.

```bash
dvc add data/
```

If Git was already tracking the folder, DVC will suggest:

```bash
git rm -r --cached data
git commit -m "Stop tracking data with Git"
```

Reason:

Git should **not track large datasets**.
Instead, **DVC will manage them**.

---

# 8. Track Data with DVC

Run again:

```bash
dvc add data/
```

This creates:

```
data.dvc
```

Add the new files to Git.

```bash
git add .gitignore data.dvc
git commit -m "Track dataset with DVC"
```

---

# 9. Push Data to Remote Storage

Now push the dataset to the remote storage.

```bash
dvc commit
dvc push
```

Explanation:

* `dvc commit` → saves changes in DVC cache
* `dvc push` → uploads data to remote storage

Now commit the version in Git.

```bash
git add .
git commit -m "Version 1 dataset"
git push
```

This marks **Version 1 (V1)** of your data.

---

# 10. Modify the Data

Update `mycode.py` to append a new row.

Example:

```python
import pandas as pd

df = pd.read_csv("data/data.csv")

new_row = {"Name": "D", "Score": 95}

df = pd.concat([df, pd.DataFrame([new_row])], ignore_index=True)

df.to_csv("data/data.csv", index=False)

print("New row added")
```

Check the changes:

```bash
dvc status
```

---

# 11. Save New Data Version

Commit and push again.

```bash
dvc commit
dvc push
```

---

# 12. Save Version in Git

```bash
git add .
git commit -m "Dataset version 2"
git push
```

Now your **second dataset version (V2)** is stored.

---

# 13. Check Status

Verify everything is synced.

```bash
git status
dvc status
```

Both should show **nothing to commit**.

---

# 14. Repeat for More Versions

For every new dataset update:

1. Modify data
2. Check status
3. `dvc commit`
4. `dvc push`
5. `git add`
6. `git commit`
7. `git push`

Example:

```
Version 1 → Initial dataset
Version 2 → Added new rows
Version 3 → Cleaned dataset
```

---

# Final Architecture

```
Project
│
├── data/
│   └── data.csv
│
├── data.dvc
├── mycode.py
├── .dvc/
└── S3/
```

---

# Key Benefits of DVC

* Version control for **large datasets**
* Works together with **Git**
* Reproducible ML pipelines
* Easy collaboration in **ML projects**
* Efficient data tracking

---

# Summary

Git tracks:

* Code
* DVC metadata

DVC tracks:

* Large datasets
* Data versions
* Storage location

Together they create a **complete ML version control system**.

