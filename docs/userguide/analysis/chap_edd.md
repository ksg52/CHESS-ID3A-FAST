# Running a CHAP EDD Pipeline (Map + Strain)

These instructions walk through copying the example pipeline files, editing
the few fields that need to change for your sample, and running the pipeline.

---

## 1. Copy the pipeline files

Copy both pipeline `.yaml` files from the example folder into your own
working directory:

```bash
cp /path/to/example/folder/*.yaml /your/working/directory/
cd /your/working/directory/
```

You should now have something like `pipeline_map.yaml` and
`pipeline_strain.yaml`.

---

## 2. Edit `pipeline_map.yaml`

### Update the `.par` file

```yaml
map:
  - edd.EddMapReader:
      filename: /nfs/chess/aux/cycles/.../raw_data/<sample-name>/<scan-name>.par
```

Point this to **your** sample's `.par` file. To find it:

```bash
find /nfs/chess/aux/cycles/2026-2/id1a3/<your-experiment-folder>/raw_data/ -name "*.par"
```

Leave the detector config (`eta` angles) unchanged unless your detector
setup differs from the example.

---

## 3. Edit `pipeline_strain.yaml`

### Update the calibration file path

```yaml
- common.YAMLReader:
    filename: /nfs/chess/aux/reduced_data/cycles/.../tth_calibration_result.yaml
```

Point this to the `tth_calibration_result.yaml` from **your** calibration run
(the CeO2 calibration scan associated with your experiment). To find it:

```bash
find /nfs/chess/aux/reduced_data/cycles/2026-2/id1a3/<your-experiment-folder>/ -name "tth_calibration_result.yaml"
```

### Check the material parameters

```yaml
materials:
  - material_name: CuNi
    sgnum: 225
    lattice_parameters: [3.585067]
```

`material_name`, `sgnum` (space group number), and `lattice_parameters`
should reflect **your** sample's material. If unsure, ask whoever set up the
experiment — using the wrong material here gives incorrect strain results.

### Detector list

The list of detector `id`s (and which are commented out) should match
between `pipeline_map.yaml` and `pipeline_strain.yaml`.

---

## 4. Update the output directory (recommended)

In each YAML's `config:` block:

```yaml
config:
  outputdir: output_<your_name_or_sample>/
  interactive: false
  log_level: info
```

Using a unique `outputdir` avoids overwriting someone else's results if
you're sharing the working directory.

---

## 5. Activate the CHAP environment

```bash
source /nfs/chess/sw/miniconda3_msnc/bin/activate
conda activate chap_dev
```

---

## 6. Run the pipelines in order

```bash
run_chap_edd_dev pipeline_map.yaml
run_chap_edd_dev pipeline_strain.yaml
```

The map step produces `map.nxs`, which the strain step reads as input — it
**must** be run first and must be in the same working directory (or update
`common.NexusReader.filename` in `pipeline_strain.yaml` to point to it).

---

## 7. Check the output

With `interactive: false`, the pipeline runs straight through without
popping up plots. Check:

- The terminal for errors / tracebacks
- `output_<...>/strain_full.nxs` — the main strain results
- `output_<...>/saved_figures/` — diagnostic plots (peak fits, etc.)

---

## Summary of what to change per run

| File                    | Field                       | Set it to                           |
|-------------------------|-----------------------------|--------------------------------------|
| `pipeline_map.yaml`     | `EddMapReader.filename`     | your sample's `.par` file             |
| `pipeline_strain.yaml`  | `YAMLReader.filename`       | your `tth_calibration_result.yaml`    |
| `pipeline_strain.yaml`  | `materials`                 | your sample's material/lattice params |
| both                    | `config.outputdir`          | a unique output folder name           |

Everything else (detector configs, `find_peak_cutoff`,
`rel_height_cutoff`, etc.) should stay as the example provides unless you
have a specific reason to change it.

---

## Troubleshooting

- **`command not found: run_chap_edd_dev`** — the conda environment isn't
  activated. Run `which CHAP` and `conda env list` to check.
- **File not found errors** — paths are relative to where you run the
  command from. Either `cd` into the right directory first, or use absolute
  paths in the YAML.
- **Plots pop up and block execution** — set `interactive: false` (already
  set above), or if you need interactive picking over SSH, set
  `MPLBACKEND=WebAgg` and use the browser-forwarded plot window.
- **Long-running jobs** — run inside `tmux` so the job survives SSH
  disconnects:

  ```bash
  tmux new -s chap
  conda activate chap_dev
  run_chap_edd_dev pipeline_map.yaml
  # Ctrl-b then d to detach; reattach later with: tmux attach -t chap
  ```
