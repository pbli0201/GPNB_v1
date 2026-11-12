# GPNB_v1

**Genomic Prediction Network Builder**

GPNB is a graphical workbench for genomic prediction. It allows researchers to import genotype and phenotype data, configure neural network architectures, train and evaluate models, inspect results, and predict traits for new individuals through a desktop interface. It is intended for researchers in genetics and breeding who want to work with configurable prediction networks without writing model code.

The current distribution provides Windows x64 installation and launch scripts, a Python/PyTorch application, English and Chinese user manuals, and four dataset archives.

## Contents

- [Features](#features)
- [Downloads and documentation](#downloads-and-documentation)
- [System requirements](#system-requirements)
- [Installation and launch](#installation-and-launch)
- [Typical workflow](#typical-workflow)
- [Input data](#input-data)
- [Results and model reuse](#results-and-model-reuse)
- [Troubleshooting](#troubleshooting)
- [License](#license)
- [Questions and bug reports](#questions-and-bug-reports)

## Features

- **Data and project management:** configure genotype and phenotype inputs, select traits, match sample identifiers, and save project configurations.
- **Visual network configuration:** build and edit networks from supported components, use predefined templates, and inspect network structure and dimensions.
- **Training and evaluation:** configure CPU or NVIDIA GPU execution, training parameters, random seeds, evaluation splits, cross-validation, and early stopping.
- **Run monitoring and results:** inspect training progress, logs, prediction metrics, and result visualizations; export results for further analysis.
- **Prediction for new individuals:** load an exported model package and apply it to compatible genotype data from a new cohort.
- **Bilingual interface:** use the application in English or Chinese.

The supplied manuals describe the available components, parameters, and workflow limitations. Literature-informed templates are GPNB implementations; their presence does not by itself establish reproduction of an original publication's results.

## Downloads and documentation

| File | Contents |
| --- | --- |
| [GPNB_v1.zip](GPNB_v1.zip) | Application source code, pinned dependency specifications, and Windows setup/start scripts |
| [English user manual](GPNB_v1_User_Manual.en.pdf) | Detailed installation instructions and operating tutorial |
| [中文用户手册](GPNB_v1_User_Manual.zh-CN.pdf) | 中文安装说明与操作教程 |
| [pig.zip](pig.zip) | Pig dataset |
| [chicken.zip](chicken.zip) | Chicken dataset |
| [soy.zip](soy.zip) | Soybean dataset |
| [spruce.zip](spruce.zip) | Spruce dataset |

Open a file link and use GitHub's **Download raw file** control to download it. Alternatively, select **Code → Download ZIP** on the repository homepage to download the entire repository. In that case, extract the repository archive first and then extract the enclosed `GPNB_v1.zip`.

Dataset archives are separate from the application. Download only the datasets you need, and extract them before selecting input files in the interface. These are research datasets, not small synthetic installation tests; training time depends on the data, model, and hardware.

## System requirements

| Item | Requirement |
| --- | --- |
| Operating system | Windows x64 with Windows PowerShell |
| First installation | Internet access and write permission in the extracted application folder |
| CPU installation | At least 8 GiB of free disk space, as required by the installer |
| GPU installation | At least 25 GiB of free disk space, a compatible NVIDIA GPU, and an installed NVIDIA driver |
| Python | Python 3.12.11 is provisioned locally by setup; a separate Python installation is not required |
| Main software dependencies | PyTorch 2.7.1, NumPy 1.26.4, PySide6 6.8.3, bed-reader 1.0.6, Matplotlib 3.10.1, and psutil 7.0.0 |

The disk-space thresholds cover environment installation; datasets and analysis outputs require additional space. Memory and GPU memory requirements depend on the dataset and network. The supplied installers target Windows; installation on Linux or macOS is not documented for this distribution.

## Installation and launch

### 1. Extract the application

Download and fully extract [GPNB_v1.zip](GPNB_v1.zip) into a writable location. Open the extracted `GPNB_v1` folder containing `Setup_GPNB_v1.cmd`, `Start_GPNB_v1.cmd`, `scripts`, `requirements`, and `src`.

Keep this folder structure intact. Run the following commands in **PowerShell opened in that folder**.

### 2. Install an environment

For a CPU installation:

```powershell
.\Setup_GPNB_v1.cmd -Profile cpu -Language en_US
```

For an NVIDIA GPU installation, choose one of the available profiles:

```powershell
# PyTorch with CUDA 12.6
.\Setup_GPNB_v1.cmd -Profile cuda -Language en_US

# Alternative: PyTorch with CUDA 11.8
.\Setup_GPNB_v1.cmd -Profile cuda118 -Language en_US
```

You can also run `.\Setup_GPNB_v1.cmd -Language en_US` to select a profile interactively. Setup checks the environment and performs a computation check before marking it ready. A successful installation ends with `[5/5] Ready.` Installation logs and diagnostic reports are written to the application's `logs` folder.

Setup creates a local environment under the application folder. It does not install or update NVIDIA drivers. The first installation downloads dependencies; setup duration has not been specified here.

### 3. Start GPNB

To launch the CPU profile in English:

```powershell
.\Start_GPNB_v1.cmd -Profile cpu -Language en_US
```

Replace `cpu` with `cuda` or `cuda118` to use the corresponding installed profile. To launch the most recently configured profile:

```powershell
.\Start_GPNB_v1.cmd -Language en_US
```

Double-clicking the setup or start scripts without arguments uses Chinese by default. Use `-Language zh_CN` explicitly for Chinese, or change the language in the interface.

After installation, use the start script for subsequent sessions. Normal application startup does not require downloading the environment again. Select a training or prediction device in the interface that is supported by the active environment; switching a launch profile does not automatically change a saved project's device setting.

## Typical workflow

| Page | What to do |
| --- | --- |
| **Data and project** | Create or open a project, select an output directory, choose genotype and phenotype files, configure ID matching, and select the target trait. |
| **Network builder** | Choose a template or configure a network using the supported components. Check the input representation and network dimensions. |
| **Training settings** | Configure the evaluation method, data split, random seeds, device, optimizer, batch size, training duration, and early stopping. |
| **Run monitor** | Follow the progress and inspect diagnostic messages while the analysis runs. |
| **Training results** | Review evaluation results, prediction tables, and plots; export outputs and model packages as appropriate. |
| **New individual prediction** | Load a compatible model package, provide the new cohort's genotype files, and choose where to save predictions. |

For a first analysis, follow the worked instructions in the [English user manual](GPNB_v1_User_Manual.en.pdf). Confirm data validation and model compatibility before starting training. The manuals provide the detailed steps and parameter explanations that are intentionally kept out of this overview.

## Input data

GPNB supports PLINK binary genotype files and explicitly configured text genotype matrices.

- **PLINK input:** supply the matching `.bed`, `.bim`, and `.fam` files. The files must describe the same individuals and markers.
- **Text genotype matrices:** use one individual per row, identifier columns, and named marker columns. Declare the delimiter, encoding, missing-value tokens, genotype coding, and allele interpretation in the input settings. Supported hard-call codings include `0/1/2` and `-1/0/1`; a valid genotype value must not also be declared as a missing token.
- **Phenotypes:** supply a delimited text table with sample identifiers and named trait columns. Configure the delimiter, encoding, ID columns, and missing-value tokens to match the file.
- **ID matching:** choose the appropriate `FID + IID` or `IID` matching mode. Matching is identifier-based; simply arranging the two files in the same row order is insufficient. Duplicate or inconsistent identifiers need to be resolved before analysis.

The file extension alone does not define the data layout. See the manual for format examples and the requirements for additional inputs and specialized network components.

## Results and model reuse

Analyses are saved under the run output directory selected in the project. Outputs depend on the workflow and can include project/model configurations, split information, training logs, observed and predicted values, and evaluation summaries. Training and cross-validation results include files such as `predictions.csv` and `metrics.json`.

Predictive performance includes Pearson correlation and error measures such as RMSE. Pearson correlation describes association between observations and predictions; RMSE describes prediction error on the response scale used for evaluation. Interpret results together with the trait scale and the evaluation design.

Use a complete exported model package when predicting new individuals. The new inputs must satisfy the package's marker or feature requirements, including compatible identifiers and encoding. Matching only the number of columns is insufficient. New-cohort phenotypes are optional for prediction and are used for scoring when provided.

## Troubleshooting

| Problem | Suggested action |
| --- | --- |
| Setup fails or a download is interrupted | Read the latest `logs/setup-*.log`, restore the connection, and rerun setup for the same profile. |
| An installed environment is incomplete or damaged | Close GPNB and rerun setup with `-Repair`, as shown below. |
| A CUDA profile fails its computation check | Check the NVIDIA driver and the diagnostic report in `logs`; use the CPU profile if necessary. |
| No samples match or an input cannot be parsed | Check the ID mode, ID columns, duplicate IDs, delimiter, encoding, and missing-value tokens. |
| A network reports incompatible dimensions | Check the selected input representation, marker/feature count, and component settings in **Network builder**. |
| Training runs out of memory | Reduce the batch size or network size and check available RAM/GPU memory. |
| Outputs cannot be saved | Choose a writable output directory and confirm there is enough free disk space. |

CPU environment repair, from PowerShell in the application folder:

```powershell
.\Setup_GPNB_v1.cmd -Profile cpu -Repair -Language en_US
```

For an update, download the replacement application package into a separate folder, run its setup, and retain your existing projects and results. Do not copy an old runtime environment into the new folder.

## License

GPNB software is distributed under the [MIT License](LICENSE). Third-party libraries retain their respective licenses. The software license should not be interpreted as granting additional rights over third-party datasets; dataset reuse must comply with the applicable source terms.

## Questions and bug reports

Please use the repository's [issue tracker](https://github.com/pbli0201/GPNB_v1/issues). Include the application version, Windows version, environment profile, a description of the steps that caused the problem, and relevant error messages. Remove private data, credentials, and individual identifiers before sharing logs or example files.
