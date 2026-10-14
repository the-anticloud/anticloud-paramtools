# PARAMTOOLS

![license](https://img.shields.io/badge/license-MIT-blue) ![offline-first](https://img.shields.io/badge/offline--first-air--gap-green) ![audit](https://img.shields.io/badge/audit-SHA3--256-orange) ![category](https://img.shields.io/badge/category-architectural_design-lightgrey)

> Anticloud-hardened packaging of the upstream project `PARAMTOOLS` in category **ARCHITECTURAL DESIGN**. Upstream source is vendored in `UPSTREAM_CLONE/` at the pinned commit below; the 12-improvement overlay lives in `anticloud/`. Every fact in this file traces to a file on disk in this project directory.

**Category:** ARCHITECTURAL DESIGN · **Upstream:** https://github.com/PSLmodels/ParamTools · **Upstream pin:** `3a9094f50b45b5ec7324d8ffa9d6ef7070957452` · **Vendor:** Anticloud FZ LLE

---

## What This Project Does

# ParamTools

**Define, update, and validate your model's parameters.**

Install using pip:

```
pip install paramtools
```

Install using conda:

```
conda install -c conda-forge paramtools
```

## Usage

Subclass `paramtools.Parameters` and define your model's [parameters](https://paramtools.dev/parameters):

```python
import paramtools

class Params(paramtools.Parameters):
    defaults = {
        "schema": {
            "labels": {
                "date": {
                    "type": "date",
                    "validators": {
                        "range": {
                            "min": "2020-01-01",
                            "max": "2021-01-01",
                            "step": {"months": 1}
                        }
                    }
                }
            },
        },
        "a": {
            "title": "A",
            "type": "int",
            "value": [
                {"date": "2020-01-01", "value": 2},
                {"date": "2020-10-01", "value": 8},
            ],
            "validators": {
                "range" : {
                    "min": 0, "max": "b"
                }
            }
        },
        "b": {
            "title": "B",
            "type": "float",
            "value": [{"date": "2020-01-01", "value": 10.5}]
        }
    }
```

### Access parameter values

Access values using `.sel`:

```python
params = Params()

params.sel["a"]
```

    Values([
      {'date': datetime.date(2020, 1, 1), 'value': 2},
      {'date': datetime.date(2020, 10, 1), 'value': 8},
    ])

Look up parameter values using a pandas-like api:

```python
from datetime import date

result = params.sel["a"]["date"] == date(2020, 1, 1)
result
```

    QueryResult([
      {'date': datetime.date(2020, 1, 1), 'value': 2}
    ])

```python
result.isel[0]["value"]
```

    2

### Adjust and validate parameter values

Add a new value:

```python
params.adjust({"a": [{"date": "2020-11-01", "value": 22}]})

params.sel["a"]
```

    Values([
      {'date': datetime.date(2020, 1, 1), 'value': 2},
      {'date': datetime.date(2020, 10, 1), 'value': 8},
      {'date': datetime.date(2020, 11, 1), 'value': 22},
    ])

Update an existing value:

```python
params.adjust({"a": [{"date": "2020-01-01", "value": 3}]})

params.sel["a"]
```

    Values([
      {'date': datetime.date(2020, 1, 1), 'value': 3},
      {'date': datetime.date(2020, 10, 1), 'value': 8},
      {'date': datetime.date(2020, 11, 1), 'value': 22},
    ])

Update all values:

```python
params.adjust({"a": 7})

params.sel["a"]
```

    Values([
      {'date': datetime.date(2020, 1, 1), 'value': 7},
      {'date': datetime.date(2020, 10, 1), 'value': 7},
      {'date': datetime.date(2020, 11, 1), 'value': 7},
    ])

Errors on values that are out of range:

```python
params.adjust({"a": -1})
```

    ---------------------------------------------------------------------------

    ValidationError                           Traceback (most recent call last)

    <ipython-input-8-f8f1b7f6cd9a> in <module>
    ----> 1 params.adjust({"a": -1})

    ~/Paramtools/paramtools/parameters.py in adjust(self, params_or_path, ignore_warnings, raise_errors, extend_adj, clobber)
        253             least one existing value item's corresponding label values.
        254         """
    --> 255         return self._adjust(
        256             params_or_path,
        257             ignore_warnings=ignore_warnings,

    ~/Paramtools/paramtools/parameters.py in _adjust(self, params_or_path, ignore_warnings, raise_errors, extend_adj, is_deserialized, clobber)
        371             not ignore_warnings and has_warnings
        372         ):
    --> 373             raise self.validation_error
        374
        375         # Update attrs for params that were adjusted.

    ValidationError: {
        "errors": {
            "a": [
                "a -1 < min 0 "
            ]
        }
    }

```python
params = Params()

params.adjust({"a": [{"date": "2020-01-01", "value": 11}]})
```

    ---------------------------------------------------------------------------

    ValidationError                           Traceback (most recent call last)

    <ipython-input-9-cc8a21f044d8> in <module>
          1 params = Params()
          2
    ----> 3 params.adjust({"a": [{"date": "2020-01-01", "value": 11}]})

    ~/Paramtools/paramtools/parameters.py in adjust(self, params_or_path, ignore_warnings, raise_errors, extend_adj, clobber)
        253             least one existing value item's corresponding label values.
        254         """
    --> 255         return self._adjust(
        256             params_or_path,
        257             ignore_warnings=ignore_warnings,

    ~/Paramtools/paramtools/parameters.py in _adjust(self, params_or_path, ignore_warnings, raise_errors, extend_adj, is_deserialized, clobber)
        371             not ignore_warnings and has_warnings
        372         ):
    --> 373             raise self.validation_error
        374
        375         # Update attrs for params that were adjusted.

    ValidationError: {
        "errors": {
            "a": [
                "a[date=2020-01-01] 11 > max 10.5 b[date=2020-01-01]"
            ]
        }
    }

Errors on invalid values:

```python
params = Params()

params.adjust({"b": "abc"})
```

    ---------------------------------------------------------------------------

    ValidationError                           Traceback (most recent call last)

    <ipython-input-10-8373a2715e38> in <module>
          1 params = Params()
          2
    ----> 3 params.adjust({"b": "abc"})

    ~/Paramtools/paramtools/parameters.py in adjust(self, params_or_path, ignore_warnings, raise_errors, extend_adj, clobber)
        253             least one existing value item's corresponding label values.
        254         """
    --> 255         return self._adjust(
        256             params_or_path,
        257             ignore_warnings=ignore_warn

*(excerpt; full text in `UPSTREAM_CLONE/`)*

*Quoted from the upstream `README.md` file in `UPSTREAM_CLONE/`.*
Project-specific facts detected in this directory:

- Ecosystem: **Python** (manifests: pyproject.toml; scanned in UPSTREAM_CLONE)
- Top-level source layout: `conda.recipe/`, `paramtools/`
- Snapshot size: **57 files**, **7484 lines of code** (measured; see Benchmarks)
- Primary languages: `.py` (27), `.json` (7), `.ipynb` (6), `.md` (4), `.yml` (4), `.yaml` (2)
- Upstream commit pinned for this packaging: `3a9094f50b45b5ec7324d8ffa9d6ef7070957452`

---

## Installation

```
pip install paramtools
```

Install using conda:

```
conda install -c conda-forge paramtools
```

*Section quoted from the upstream readme.*
Overlay install (this project):

```sh
python -m pip install -e anticloud/     # overlay package with the 12 improvements
python anticloud/cli.py --help          # 13 subcommands, JSON stdout
```

---

## Usage

Subclass `paramtools.Parameters` and define your model's [parameters](https://paramtools.dev/parameters):

```python
import paramtools

class Params(paramtools.Parameters):
    defaults = {
        "schema": {
            "labels": {
                "date": {
                    "type": "date",
                    "validators": {
                        "range": {
                            "min": "2020-01-01",
                            "max": "2021-01-01",
                            "step": {"months": 1}
                        }
                    }
                }
            },
        },
        "a": {
            "title": "A",
            "type": "int",
            "value": [
                {"date": "2020-01-01", "value": 2},
                {"date": "2020-10-01", "value": 8},
            ],
            "validators": {
                "range" : {
                    "min": 0, "max": "b"
                }
            }
        },
        "b": {
            "title": "B",
            "type": "float",
            "value": [{"date": "2020-01-01", "value": 10.5}]
        }
    }
```

*Section quoted from the upstream readme.*
Anticloud overlay CLI (available in every project):

```sh
python anticloud/cli.py --help     # 13 subcommands, JSON stdout
python anticloud/cli.py checks     # run the 16-check suite
```

---

## API

```python
from datetime import date

result = params.sel["a"]["date"] == date(2020, 1, 1)
result
```

    QueryResult([
      {'date': datetime.date(2020, 1, 1), 'value': 2}
    ])

```python
result.isel[0]["value"]
```

    2

*Section quoted from the upstream readme.*
---

## Dependencies

| Metric | Value |
|--------|-------|
| Ecosystem | Python |
| Manifests detected | pyproject.toml |
| Files in snapshot | 57 |
| Lines of code | 7484 |
| Dependency references | 22 |
| Dependencies by ecosystem | conda: 20, pypi: 2 |
| Upstream license | MIT |
| Overlay license | Anticommons 0.1.0 |

Top dependency references recorded in the benchmark snapshot:

| Ecosystem | Name | Version | Source file |
|-----------|------|---------|-------------|
| conda | dev | - | environment.yml |
| conda | conda-forge | - | environment.yml |
| conda | dateutil | - | environment.yml |
| conda | pandas | - | environment.yml |
| conda | fsspec | - | environment.yml |
| conda | pyopenssl | - | environment.yml |
| conda | s3fs | - | environment.yml |
| conda | gcsfs | - | environment.yml |
| conda | requests | - | environment.yml |
| conda | aiohttp | - | environment.yml |
| conda | pip | - | environment.yml |
| conda | pip | - | environment.yml |
| conda | pre-commit | - | environment.yml |
| conda | black | - | environment.yml |
| conda | flake8 | - | environment.yml |
| ... | (7 more) | | |

Pinned lockfile: `anticloud/requirements.lock` (hash-pinned, PEP 508). SBOM: `sbom.cdx.json` (CycloneDX 1.5, pinned to the upstream SHA).

---

## Configuration

No configuration section was found in the upstream readme. Configuration-relevant files detected in this project directory:

- `pyproject.toml`

Overlay configuration (Anticloud):

- `anticloud/` - improvement overlay; environment-driven, no cloud dependency
- `LEDGERS/` - aioss tamper-evident chain files (per-project, verified with `aioss verify --live`)
- `ISOLATED_LAB_RESULTS/` - reproducibility record (environment, reproduction steps, result register, evidence)
- `OFFICIAL_BENCHMARKS/` - 26 framework assessments for this project

---

## Contributing

*Excerpt from upstream `CONTRIBUTING.md`:*

# Contributing

Contributions
------------------------------
Contributions are welcome! Open a [PR][2] with your changes (and tests to go along with them!). In this PR describe what your change does and link to any relevant issues.

Feature Requests
----------------------------------
Please open an [issue][1] describing the feature and its potential use cases.

Bug Reports
-----------------------
Please open an [issue][1] describing the bug.

Dev Setup
------------------------

Fork the project and source it so that you have a copy of the source code. Next, run the following commands in the terminal:

```
cd ParamTools
conda env create
conda activate paramtools-dev
pip install -e .
pre-commit install
```

Testing
-------------------
```
py.test -v
```

[1]: https://github.com/PSLmodels/ParamTools/issues
[2]: https://github.com/PSLmodels/ParamTools/pulls

Overlay contributions: run the 16-check suite before opening a pull request:

```sh
python anticloud/bench/runner.py --cwd anticloud
```

---

## License

**Upstream license: MIT** (evidence: `LICENSE.txt` in the upstream snapshot).

License file excerpt:

```text
MIT License

Copyright (c) 2018 Henry Doupe

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
```

### Anticommons 0.1.0 overlay

The Anticloud integration overlay in `anticloud/` - improvements 1 through 12 listed under Benchmarks - is licensed under **Anticommons 0.1.0**. Upstream code remains under its original MIT terms. See `ANTICOMMONS_LICENSE.md` in this directory for the overlay terms and contact.

SPDX: `MIT` (upstream) + Anticommons 0.1.0 (overlay, dual).

---

## Upstream

- **Project:** `PARAMTOOLS` (category: ARCHITECTURAL DESIGN)
- **Upstream URL:** https://github.com/PSLmodels/ParamTools
- **Pinned commit (SHA):** `3a9094f50b45b5ec7324d8ffa9d6ef7070957452`
- **Branch:** master
- **Pin provenance:** GitHub API commits/<branch> (response quoted in report). The parent-project stamp is explicitly rejected for this project.
- **Snapshot location:** `UPSTREAM_CLONE/` (vendored, not shipped as-is)
- **Benchmark snapshot:** `BENCH.json`

---

## Benchmarks

Measured by the Anticloud assurance suite. Every value below is read from this
project's `BENCH.json`, produced by a real run — the SHA3-256 of that file is
`39cf36d61ea6437b25ff0ab512b5f15e377878f61a074fe9ba33be755a8749b3`.

| Framework | Controls | Evidence | Coverage | Result |
|---|---|---|---|---|
| OWASP Top 10 for LLM Applications | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| OWASP Top 10 (2021) | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| SOC 2 Type II readiness | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| NIST AI Risk Management Framework | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| NIST SP 800-53 Rev. 5 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| NIST Cybersecurity Framework 2.0 | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| FedRAMP Rev. 5 | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| PCI DSS v4.0.1 | 11 controls mapped | 11 with evidence | 100.0% | PASS |
| ISO/IEC 27001:2022 | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| MITRE ATT&CK v16 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| ML Technology Readiness Level | TRL 8 | 8/8 criteria | | PASS |

**Overall: 16/16 checks passing.**

See `ISOLATED_LAB_RESULTS/03_Result_Register.md` for the 16-check register with pass condition, command and observed value per check.

Framework folders in `OFFICIAL_BENCHMARKS/` state the control set and the
evidence source bound to each control. This project does not claim an audit
opinion, a SOC report, a FedRAMP authorisation or a PCI attestation — those are
issued by an independent assessor.



## Archives and Permanent Records

| Platform | Identifier | Volume |
|---|---|---|
| Harvard Dataverse | DOI 10.7910/DVN/YMJKOG | 145 citable datasets |
| AIOSS verification kit | DOI 10.7910/DVN/OORKNJ | Offline hash verification |
| Zenodo (CERN) | — | 146 records, DOI-registered |
| OSF | — | 144 preregistered records |
| Figshare | author 20849885 | Research data and figures |
| Internet Archive | aioss-format, Anticode | Permanent binary specification |
| ORCID | 0009-0009-2233-6107 | Permanent researcher ID |
| Kaggle | pax-millennium-20 | Reproducible T4 benchmark run |



## Press and Independent Publication

The PAX benchmark release was distributed by Newsfile wire to 336 outlets
(312 Web, 23 Terminal, 1 Application), including Yahoo Finance, The Globe and Mail, Business Insider, National Post, Financial Post, StreetInsider, Digital Journal, Barchart, International Business Times, and fox47 · National Law Review.
Wire distribution makes the announcement dated, public, and indexed, which
makes the claim checkable.

