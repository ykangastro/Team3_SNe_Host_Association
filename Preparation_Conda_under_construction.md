# NOT TRY YET OCT 8, 2026 - need to check. 
Workshop preparation: isolated conda environments 

This guide covers the tools in [Preparation.md](https://github.com/ykangastro/Team3_SNe_Host_Matching/blob/main/Preparation.md).

Use a new environment for this workshop. Conda manages Python; pip manages the Python packages inside each environment. Install related packages together so pip can resolve their dependencies in one operation.

The main workflow and DELIGHT requirement sets below passed dependency resolution for Python 3.11 using package metadata retrieved on 8 October 2026. This is a resolver check, **not a full installation or scientific validation**, and platform-specific dependencies may differ. No recipe can guarantee compatibility on every computer.

## 1. Choose the environments

| Tool | Environment or access method |
| --- | --- |
| ALeRCE, Lasair | `sne-host` |
| LSDB, Pröst, Data Lab Python client | `sne-host` |
| LightCurveLynx | `sne-host`, basic installation |
| Fink | `sne-host`, optional client |
| DELIGHT | `sne-delight`, separate TensorFlow environment |
| FrankenBlast | `sne-frankenblast`, upstream Python 3.10 environment |
| SNID SAGE | `sne-snid`, separate GUI environment |
| Blast | Browser, or Docker for a local deployment |
| Data Lab website, TNS–EDP2 explorer | Browser; no Python installation required |

You only need one host-association method to start: Pröst is included in the main environment. Add DELIGHT if you want to compare methods.

## 2. Before installing

Install [Miniforge](https://github.com/conda-forge/miniforge#download) if you do not already have conda. An existing Miniconda or Anaconda installation is also usable; a second conda installation is unnecessary.

Open Terminal on macOS/Linux, or a conda-enabled prompt on Windows:

```bash
conda --version
conda info
conda env list
```

On an Apple Silicon Mac, use the native `osx-arm64` installation. Keep Python, conda, and compiled packages on the same architecture; avoid mixing a Rosetta Intel environment with native ARM packages.

For the commands below:

- Do not install workshop packages in `base` or an existing research environment.
- Do not activate a `venv` inside the conda environment.
- Always use `python -m pip`, so installation follows the activated Python.
- Do not use `sudo pip`, `pip --user`, or `--no-deps`.
- Install Python and pip with conda first. After pip installs the scientific stack, recreate the environment if you need to change its conda-managed components.
- If an environment name already exists, choose a new name instead of installing over it.

On Rubin RSP, preserve the supplied Rubin/LSST stack. Run these local setup instructions on your laptop or in an independently supported custom environment; installing packages does not grant Rubin data rights or provide the Rubin Butler stack.

## 3. Main environment: brokers, catalogs, Pröst, and simulations

### Create and activate

```bash
conda create --name sne-host --override-channels -c conda-forge python=3.11 pip -y
conda activate sne-host
python -m pip install --upgrade pip
python -c "import sys; print(sys.executable); print(sys.version)"
```

The Python path should contain the `sne-host` environment.

### Save the requirements

Create a file named `requirements-sne-host.txt` with:

```text
numpy>=2,<3
pandas>=3,<4
matplotlib>=3.9
scipy
astropy
astroquery
pyarrow
jupyterlab
ipykernel
alerce==2.3.1
lasair==0.1.4
lsdb==0.11.0
astro-prost==1.2.13
lightcurvelynx==0.6.3
astro-datalab
```

These top-level versions were available when this guide was prepared. They are not a complete lockfile: pip still chooses compatible transitive dependencies. LSDB 0.11.0 brings in HATS 0.11, which requires pandas 3. LightCurveLynx 0.6.3 requires NumPy 2. Do not add an old `pandas<3` or `numpy<2` constraint to this environment.

The Data Lab client is distributed as `astro-datalab` and imported as `dl`.

For the optional Fink streaming client, add this line **before installing**:

```text
fink-client==12.2.0
```

Then check and install the entire set:

```bash
python -m pip install --dry-run -r requirements-sne-host.txt
python -m pip install -r requirements-sne-host.txt
python -m pip check
```

If the dry run fails, resolve the reported conflict before running the installation. A successful `pip check` should report `No broken requirements found.`

The basic LightCurveLynx installation is sufficient to begin. Some notebooks require optional dependencies. If a notebook asks for extras, put those in a separate simulation environment and install them together with LightCurveLynx; the large `[all]` and `[dev]` extras are not included here.

### Check imports

```bash
python -c "import numpy, pandas, matplotlib, scipy, astropy, astroquery, pyarrow; import alerce, lasair, lsdb, astro_prost, lightcurvelynx; from dl import queryClient; print('Main environment imports OK')"
```

If Fink was included:

```bash
python -c "import fink_client; print('Fink import OK')"
```

These checks verify imports. They do not test network access, catalog queries, authentication, or host-association results.

### Register the notebook kernel

```bash
python -m ipykernel install --user --name sne-host --display-name "Python (SNe host matching)"
python -m jupyterlab
```

Select **Python (SNe host matching)** in Jupyter. Check the notebook interpreter:

```python
import sys
print(sys.executable)
```

## 4. DELIGHT: separate TensorFlow environment

DELIGHT 0.1.0 supports Keras 3 by defining the network architecture in code and loading a separate trained weights file. The upstream README explains that releases through 0.0.11 shipped a legacy Keras 2 model containing `TFOpLambda` layers. Use the current release and its current API/example notebook together.

### Create the environment

```bash
conda create --name sne-delight --override-channels -c conda-forge python=3.11 pip -y
conda activate sne-delight
python -m pip install --upgrade pip
```

Create `requirements-sne-delight.txt`:

```text
astro-delight==0.1.0
tensorflow==2.21.0
numpy>=2,<3
pandas>=2.2,<3
jupyterlab
ipykernel
```

Install as one operation:

```bash
python -m pip install --dry-run -r requirements-sne-delight.txt
python -m pip install -r requirements-sne-delight.txt
python -m pip check
```

Let TensorFlow select compatible Keras, h5py, and ml-dtypes versions. Do not independently force older versions of those dependencies.

The published TensorFlow 2.21 Python 3.11 wheels include Apple Silicon macOS, Linux ARM/x86, and Windows x86. A matching macOS Intel wheel was not listed in the metadata retrieved for that release. If pip reports no matching distribution, use the upstream DELIGHT Colab notebook or a compatible Linux environment rather than forcing incompatible wheels. GPU plugins are not required for this CPU-first setup.

### Check TensorFlow and the packaged model

```bash
python -c "import tensorflow as tf; print(tf.__version__); print(tf.reduce_sum(tf.constant([1., 2.])).numpy())"
python -c "import delight; import importlib.metadata as m; print(m.version('astro-delight')); print(list(delight.__path__))"
```

Check the installed model through the package API:

```python
from delight.delight import Delight

# No image download is needed to check that the model loads.
client = Delight("./delight-installation-check", ["installation-check"], [150.7444145], [2.3434116])
client.load_model()
print("DELIGHT model loaded")
```

Follow the [current DELIGHT example notebook](https://github.com/fforster/DELIGHT/blob/main/notebook/Delight_example_notebook.ipynb) for a complete download and prediction test. A previous custom `Delight_new` implementation or Rubin cutout adaptation may require separate changes.

Register the kernel:

```bash
python -m ipykernel install --user --name sne-delight --display-name "Python (DELIGHT)"
python -m jupyterlab
```

Select **Python (DELIGHT)** for DELIGHT notebooks. Use CSV or Parquet containing transient IDs and host coordinates to exchange results with `sne-host`.

## 5. FrankenBlast: its own Python 3.10 environment

FrankenBlast has a large, tightly pinned upstream requirements file, FSPS configuration, and trained model downloads. Keep it separate from both environments above.

Run these commands from the directory where you keep external repositories:

```bash
git clone https://github.com/anugent96/frankenblast-host.git
cd frankenblast-host
conda create --name sne-frankenblast --override-channels -c conda-forge python=3.10.15 pip -y
conda activate sne-frankenblast
python -m pip install --upgrade pip
python -m pip install --dry-run -r requirements.txt
```

The upstream requirements passed a metadata resolution check for Python 3.10. This does not establish that all pinned wheels or source builds work on your operating system. In particular, this file contains platform-sensitive packages, including `appnope`, `pycurl`, GUI dependencies, and `tensorflow-io-gcs-filesystem`.

If the dry run succeeds, install:

```bash
python -m pip install -r requirements.txt
python -m pip check
```

If a platform-specific pin fails, use a compatible isolated machine/environment or resolve that pin with the maintainer. Do not remove all version pins or force installation with `--no-deps`.

Finish the application setup:

1. Install/configure FSPS using the [python-fsps instructions](https://dfm.io/python-fsps/current/installation/). A compatible compiler and the FSPS data installation may be needed.
2. Download `sbipp_phot.zip` and `sbi_training_sets.zip` from the [upstream linked Zenodo record](https://doi.org/10.5281/zenodo.16953205), place them in `data/`, and extract them before the workshop.
3. Edit the repository's `settings.sh` to point to your actual FSPS, training, photometry, output, and PROST data paths.
4. On macOS/Linux, load the settings for the current shell with `source settings.sh`. Keep these application-specific paths scoped to this shell/environment rather than adding them globally.
5. Run the [FrankenBlast tutorial](https://github.com/anugent96/frankenblast-host/blob/main/FrankenBlast%20Tutorial.ipynb), including a small end-to-end example.

```bash
python -m ipykernel install --user --name sne-frankenblast --display-name "Python (FrankenBlast)"
python -m jupyterlab
```

Use a matching kernel and load the settings before starting Jupyter. Package installation alone is insufficient for SED fitting.

## 6. SNID SAGE: separate spectral-classification environment

```bash
conda create --name sne-snid --override-channels -c conda-forge python=3.11 pip -y
conda activate sne-snid
python -m pip install --upgrade pip
python -m pip install snid-sage
python -m pip check
python -c "import snid_sage; print('SNID SAGE import OK')"
sage --help
snid-sage
```

The last command launches the GUI on a computer with a graphical desktop. The current installation documentation says the template bank downloads on first run, approximately 500 MB. Launch it before the workshop to finish that download.

For notebooks, optionally install and register `ipykernel` in this environment. See the [SNID SAGE installation guide](https://fiorenst.github.io/SNID-SAGE/installation/installation/) for platform-specific GUI troubleshooting.

## 7. Blast: use the web application or Docker

For workshop use, start with [the hosted Blast application](https://blast.scimma.org/). Local Blast is a web application with databases and background services, not a single package to add to `sne-host`.

The upstream local-development guide recommends Docker Desktop and at least 32 GB allocated memory. If you need a local deployment, install Docker Desktop and follow these commands from a separate repository directory:

```bash
git clone https://github.com/scimma/blast.git
cd blast
docker --version
docker compose version
```

Review `env/.env.default` and create `env/.env.dev` for your overrides. The development guide recommends setting `BLAST_IMAGE=blast_base` there for a local build. Populate TNS bot credentials only if you need TNS ingestion.

```bash
bash run/blastctl full_dev up
```

Open <http://localhost:8000/> after initialization. To stop the stack:

```bash
bash run/blastctl full_dev down
```

The `slim_dev` profile starts the web server and database for frontend development; use `full_dev` for the full stack. Follow the [local deployment guide](https://blast.readthedocs.io/en/latest/developer_guide/dev_running_blast.html) for initialization and service configuration.

## 8. Accounts, tokens, and browser tools

| Service | Preparation beyond installing Python packages |
| --- | --- |
| ALeRCE | Test the queries in the workshop notebook; check access requirements for the endpoint used. |
| Lasair | Obtain your API token and verify a small query. |
| Fink | Check API/stream credentials required for the intended service; a Kafka client installation does not create stream access. |
| Rubin DP2 / RSP | Verify your account and data rights; follow the LSDB Rubin DP2 tutorial for authenticated access. |
| Astro Data Lab | Register at [Data Lab](https://datalab.noirlab.edu/); configure authentication for services that require it. |
| TNS–EDP2 explorer | Open [the explorer](https://trivialtz.github.io/tns-edp2-explorer/); restricted EDP2 products still require Rubin data rights. |

For Lasair, keep tokens outside notebooks and Git. A portable way to set the token for the current Python/Jupyter session is:

```python
import getpass
import os
from lasair import lasair_client

os.environ["LASAIR_LSST_TOKEN"] = getpass.getpass("Lasair API token: ")
client = lasair_client(os.environ["LASAIR_LSST_TOKEN"])
```

## 9. Save the working versions

After each environment passes `pip check`, import checks, and its relevant tutorial, activate that environment and save its package list. For example:

```bash
conda activate sne-host
python -m pip freeze > requirements-sne-host-lock.txt
conda env export > environment-sne-host-resolved.yml
```

Repeat with distinct filenames for DELIGHT, FrankenBlast, and SNID SAGE.

The frozen pip list is a version snapshot, not a cross-platform binary lock. Conda exports may include platform-specific builds and a local `prefix:` line; remove the local prefix before sharing. For a fresh environment on a compatible platform, install the saved pip list after creating the same Python version with conda.

## 10. Troubleshooting

| Symptom | Action |
| --- | --- |
| `ModuleNotFoundError` in a notebook | Check `sys.executable`; select the registered kernel for that tool and restart the kernel. |
| `numpy.dtype size changed` / NumPy ABI error | Recreate a clean environment and install the requirement set together. Check that old user-site packages or a custom `PYTHONPATH` are not shadowing it. |
| Resolver conflict involving pandas | The latest LSDB stack requires pandas 3; use the main requirements together rather than adding older pandas pins. |
| DELIGHT legacy `.h5` / `TFOpLambda` loading error | Check that `astro-delight==0.1.0` is installed and use its model loader/current example. |
| TensorFlow exits or crashes | Test the standalone TensorFlow command outside Jupyter. Confirm architecture and wheel support; use Colab/Linux if the local runtime still fails. |
| FrankenBlast installs but SED fitting fails | Check FSPS, model downloads, and every path in `settings.sh`. |
| No matching distribution | Check Python version, OS, architecture, and that the specified release is available on the configured package index. Do not bypass dependency checks. |
| `pip check` passes but a query fails | Test credentials, network access, endpoint availability, and data coverage; dependency resolution does not validate services. |

## Sources

- [Conda environment management and pip guidance](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
- [ALeRCE client](https://alerce.readthedocs.io/en/latest/)
- [Lasair client](https://lasair-lsst.readthedocs.io/en/main/core_functions/client.html)
- [Fink client](https://doc.ztf.fink-broker.org/services/fink_client/)
- [LSDB installation](https://docs.lsdb.io/en/latest/getting-started.html) and [Rubin DP2 tutorial](https://docs.lsdb.io/en/latest/tutorials/rubin_dp2_release.html)
- [LightCurveLynx installation](https://lightcurvelynx.readthedocs.io/en/latest/index.html#installation)
- [Pröst](https://astro-prost.readthedocs.io/en/latest/)
- [DELIGHT README and model compatibility notes](https://github.com/fforster/DELIGHT)
- [FrankenBlast README, requirements, and settings](https://github.com/anugent96/frankenblast-host)
- [Blast local deployment](https://blast.readthedocs.io/en/latest/developer_guide/dev_running_blast.html)
- [SNID SAGE installation](https://fiorenst.github.io/SNID-SAGE/installation/installation/)

The pinned package metadata can be checked at the relevant PyPI pages: [ALeRCE](https://pypi.org/project/alerce/), [Lasair](https://pypi.org/project/lasair/), [LSDB](https://pypi.org/project/lsdb/), [Pröst](https://pypi.org/project/astro-prost/), [LightCurveLynx](https://pypi.org/project/lightcurvelynx/), [Fink client](https://pypi.org/project/fink-client/), [DELIGHT](https://pypi.org/project/astro-delight/), and [TensorFlow](https://pypi.org/project/tensorflow/).
