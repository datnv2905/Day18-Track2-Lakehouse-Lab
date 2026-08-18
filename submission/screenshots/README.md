# Storage-layer evidence — lightweight path

`rubric.md` accepts either of two proofs for the storage layer. This submission
ran the **lightweight path** (`deltalake` + `pyiceberg`, no Docker/MinIO), so it
supplies the second option: *"`tree _lakehouse/` plus the contents of one
`_delta_log/*.json`"*.

| File | What it shows |
|---|---|
| `01_tree_lakehouse.txt` | Full `_lakehouse/` layout — 1,236 files / 111.2 MB. Bronze, Silver, Gold all present; Gold partitioned `date=2026-04-01 … 04-08`; three isolated Iceberg catalogs (`nb5`, `nb6`, `nb8`). |
| `02_delta_log_commit.txt` | Commit `v0` of the NB1 table, all four actions: `commitInfo`, `protocol`, `metaData`, `add` — including the per-file `stats` (min/max/nullCount) that make the Z-ORDER pruning in NB2 and the clustering skip-rate in NB6 measurable. |
| `03_nb6_jupyter_pass.png` | Jupyter Lab running `06_maintenance.ipynb` — the final assert cell with all nine `[PASS]` lines, including `iceberg stranded files swept`. |

All three were generated from the same run that produced the executed notebooks.
