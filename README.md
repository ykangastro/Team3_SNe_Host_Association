## Basic Git Commands

### 1. Clone the repository

To download this repository to your local machine:

```bash
git clone https://github.com/ykangastro/Team3_SNe_Host_Matching.git
```

Then move into the repository directory:

```bash
cd Team3_SNe_Host_Matching
```

When you use `git clone`, the GitHub repository is automatically registered as the remote repository named `origin`.

You can check the remote repository with:

```bash
git remote -v
```

You should see something similar to:

```text
origin  https://github.com/ykangastro/Team3_SNe_Host_Matching.git (fetch)
origin  https://github.com/ykangastro/Team3_SNe_Host_Matching.git (push)
```

### 2. Connect an existing local repository to this GitHub repository

If you already have a local Git repository and did not use `git clone`, connect it to this repository using:

```bash
git remote add origin https://github.com/ykangastro/Team3_SNe_Host_Matching.git
```

Check that the remote was added correctly:

```bash
git remote -v
```

### 3. Pull the latest changes

Before starting work, pull the latest version from the remote repository:

```bash
git pull origin main
```

### 4. Check modified files

To check which files have been changed:

```bash
git status
```

### 5. Add changes

Add all modified files:

```bash
git add .
```

Or add a specific file:

```bash
git add filename.py
```

### 6. Commit changes

Save the changes to the local Git history with a descriptive commit message:

```bash
git commit -m "Description of the changes"
```

For example:

```bash
git commit -m "Update host matching analysis"
```

### 7. Push changes to GitHub

Push the committed changes to the remote repository:

```bash
git push origin main
```

If the local branch is already connected to the remote branch, you can simply use:

```bash
git push
```

### Typical workflow

After cloning the repository, the usual workflow is:

```bash
git pull origin main

# Edit or create files

git status
git add .
git commit -m "Describe the changes"
git push origin main
```

It is recommended to run `git pull` before starting work and before pushing changes, especially when multiple people are working on the repository.
