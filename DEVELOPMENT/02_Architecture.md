# Technical Architecture — PARAMTOOLS

**Upstream:** [https://github.com/paramtools/paramtools](https://github.com/paramtools/paramtools)
**License:** MIT
**Category:** ARCHITECTURAL_DESIGN
**Anticloud Integration:** PAX L5 Narrow L2 General 27B + AIOSS + Offline-First

## Upstream Description

Parameter tools for architectural simulation

## Anticloud Architectural Changes

1. PAX L5 Narrow L2 General 27B local generative design and code compliance checking
2. AIOSS version chain for all design iterations with cryptographic proof
3. AES-256 encryption for client design files and proposals
4. Single-binary desktop tool replacing cloud-dependent design software
5. Offline rendering pipeline: ray tracing on local GPU without cloud render farm
6. GPU/CPU equalizer: full offline rendering from laptop CPU to workstation GPU
7. Zero-cloud client presentation: all assets served locally
8. Open format export: removes proprietary lock-in, outputs to IFC/DWG/PDF

## Integration Points

- **PAX Inference Socket:** Local HTTP endpoint at `127.0.0.1:11434/v1/chat` — same OpenAI-compatible API, zero cloud
- **AIOSS Hook:** Every write operation calls `aioss_append(event, payload)` before commit
- **Encryption Layer:** All file I/O routed through `anticloud_crypto.encrypt_at_rest()`
- **Single Binary Build:** `pyinstaller anticloud_paramtools.spec` or `go build -o paramtools`

## Deployment Modes

| Mode | Hardware | Notes |
| --- | --- | --- |
| Edge CPU | Raspberry Pi 4 / Intel NUC | Full feature set, PAX on CPU |
| Desktop GPU | RTX 3060 / A10 | PAX GPU inference, <1s latency |
| Server | 2× A100 | Full batch throughput |
| Air-gapped | Any x86/ARM | Zero network dependency |