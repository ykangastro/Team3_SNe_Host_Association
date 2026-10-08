# Environment Setup (uv)

This repository uses [uv](https://docs.astral.sh/uv/) to manage a shared Python environment, so that everyone on the team works with **the same Python version and the same package versions**.

The environment is defined by three files in the repository:

| File | What it does |
|---|---|
| `pyproject.toml` | Lists the packages the project needs |
| `uv.lock` | Pins the exact version of every package (do not edit by hand) |
| `.python-version` | Fixes the Python version (3.12) |

The environment itself lives in a local `.venv/` folder, which is **not** uploaded to GitHub.

---

## 1. Install uv

**Linux / macOS**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows (PowerShell)**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Close and reopen your terminal, then check it works:
```bash
uv --version
```

> uv will download Python 3.12 automatically if you don't have it. You don't need to install Python separately.

---

## 2. Clone the repository and create the environment

```bash
git clone https://github.com/ykangastro/Team3_SNe_Host_Matching.git
cd Team3_SNe_Host_Matching
uv sync
```

`uv sync` creates the `.venv/` folder and installs all the essential packages plus the development tools (Jupyter, etc.). The first run may take a few minutes.

If you are using conda, **deactivate it first** (`conda deactivate`) so the two don't get mixed up.

---

## 3. Check the installation

```bash
uv run python -c "import alerce, lasair, lsdb, astro_prost, delight; print('ok')"
```

If it prints `ok`, you are ready.

---

## 4. What is installed

**Essential (installed by default with `uv sync`)**
- Alert brokers: `alerce`, `lasair`
- Catalogs and crossmatching: `lsdb`
- Host galaxy association: `astro-delight` (DeLight), `astro-prost` (Pröst)
- Basics: `astropy`, `numpy`, `pandas`, `scipy`, `matplotlib`
- `pycurl` (required by Pröst through the DataLab client)

**Development tools (installed by default)**
- `jupyterlab`, `ipykernel`, `ipywidgets`

**Optional group `extras` (not installed by default)**
- `lightcurvelynx`, `fink-client`

To install the optional group:
```bash
uv sync --group extras
```

**Not included in the environment:** Blast and Frankenblast (Frankenblast needs ~2.5 GB of models; install it separately only if you plan to use it), DataLab (web platform, only needs an account), and SNID SAGE. See `Preparation.md` for their links.

---

## 5. Using the environment

### In VS Code (recommended)
1. Install the **Python** and **Jupyter** extensions.
2. Open the **repository folder** (`Team3_SNe_Host_Matching`) in VS Code.
3. In a notebook: click **Select Kernel** (top right) → **Python Environments** → choose `.venv`.
4. For `.py` scripts: `Ctrl/Cmd + Shift + P` → **Python: Select Interpreter** → choose `.venv`.

Then just use the ▶ button as usual.

To confirm a notebook is using the right environment, run in a cell:
```python
import sys; print(sys.executable)
```
The path should end in `.venv/...`.

### From the terminal
Run anything inside the environment with `uv run`, no activation needed:
```bash
uv run python my_script.py
uv run jupyter lab
```

If you prefer to activate the environment:
```bash
source .venv/bin/activate          # Linux / macOS (bash, zsh)
source .venv/bin/activate.fish     # Linux / macOS (fish)
.venv\Scripts\activate             # Windows
```
Use `deactivate` to exit.

---

## 6. Team rules

1. **After every `git pull`, run `uv sync`.** Someone may have added a package.
2. **To add a package, use `uv add`, never `pip install`.**
   ```bash
   uv add package-name                      # essential package
   uv add --group extras package-name       # optional package
   ```
   Then commit **both** `pyproject.toml` and `uv.lock` in the same commit.
3. **Never upload `.venv/`.** It is excluded by `.gitignore`. If you use `git add .`, check with `git status` first that `.venv/` does not appear.
4. **Don't change the Python version** without telling the team (see the next section).

---

## 7. Changing the Python version

The project uses **Python 3.12**. Only change it if a package we need doesn't work with this version, and **agree on it with the team first**, since it affects everyone's environment.

To test a different version safely, work on a separate branch:

```bash
git checkout -b test/python-3.13          # example for 3.13
uv python pin 3.13                        # updates .python-version
```

Open `pyproject.toml` and update the `requires-python` line so it matches, for example:
```toml
requires-python = ">=3.13"
```

Then regenerate the lock file and rebuild the environment:
```bash
uv lock
uv sync
uv run python -c "import alerce, lasair, lsdb, astro_prost, delight; print('ok')"
```

- If `uv lock` fails, some package doesn't support that version yet. Go back with `git checkout main` and delete the branch with `git branch -D test/python-3.13`.
- If everything works, commit **all three files together** (`.python-version`, `pyproject.toml`, `uv.lock`) and let the team know. After pulling the change, everyone just runs `uv sync`.

---

## 8. Troubleshooting

**A notebook can't find a package I just installed**
Restart the kernel (Restart button in the notebook toolbar).

**`.venv` doesn't appear in VS Code's kernel list**
Click the refresh icon in the list, or reopen VS Code. If it still doesn't appear, choose "Enter interpreter path" and select:
- Linux / macOS: `.venv/bin/python`
- Windows: `.venv\Scripts\python.exe`

**`pycurl` fails to install**
`pycurl` needs the system library libcurl.
- Ubuntu / Debian: `sudo apt install libcurl4-openssl-dev libssl-dev`, then run `uv sync` again.
- macOS / Windows: this can be harder; please tell the team in the chat so we can solve it together.

**The wrong Python is being used**
Make sure conda is deactivated (`conda deactivate`) and that you are inside the repository folder.

**Merge conflict in `uv.lock`**
Don't edit it by hand. Keep the version from `main` and run `uv lock` to regenerate it, then commit the result.

**I want to start from scratch**
Delete the `.venv/` folder and run `uv sync` again.
