# Build and Test

**Project:** `PARAMTOOLS`
**Upstream:** https://github.com/paramtools/paramtools
**License:** MIT

## Quick Start

```bash
git clone https://github.com/paramtools/paramtools
cd paramtools
pip install -r requirements-anticloud.txt
python anticloud_main.py --offline --pax-local
```

## Anticloud Improvements Applied

1. PAX L5 Narrow L2 General 27B local generative design and code compliance checking
2. AIOSS version chain for all design iterations with cryptographic proof
3. AES-256 encryption for client design files and proposals
4. Single-binary desktop tool replacing cloud-dependent design software
5. Offline rendering pipeline: ray tracing on local GPU without cloud render farm
6. GPU/CPU equalizer: full offline rendering from laptop CPU to workstation GPU
7. Zero-cloud client presentation: all assets served locally
8. Open format export: removes proprietary lock-in, outputs to IFC/DWG/PDF

## Benchmark Targets

| Metric | Target |
| --- | --- |
| Latency | Primary inference task: <5s on CPU, <1s on GPU |
| Throughput | Batch processing: >100 items/hour on single CPU server |
| Memory | <8GB RAM for standard deployment |
| Accuracy | Task-specific accuracy within 5% of cloud-API baseline |

## Build Status

Not yet measured. Run verified build and record actual figures above.
