# Far-field frame caches (Dexela)

Far-field HEDM scans on the Dexela (ff1 + ff2) are turned into hexrd frame
caches: for each panel, the median of the first 100 frames is subtracted as
the dark, the panel gaps and flips are applied, and only pixels above a
threshold are kept. This runs automatically on the compute farm after each
scan taken in ff mode with the Dexela that covers more than 90° of omega
(shorter scans, nf scans and other detectors are skipped). Scans can also be
(re)run by hand, from a list of spec scans or par files.

The scripts come with the `fast-python-scripts` folder, which is already set
up in your experiment's `reduced_data` folder. Check that it is there:

    ls /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data/fast-python-scripts

## 1. Make the config file

Each experiment needs one YAML config in its `reduced_data` folder. Copy the
template from `fast-python-scripts` and edit it:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    cp fast-python-scripts/data_analysis/hedm/ff/example_config.yaml dex_ff_config.yaml

The template comments explain every setting; the main ones are:

| Setting | Meaning |
|---|---|
| `cycle`, `beamline`, `btr` | Where the raw data are: `/nfs/chess/raw/<cycle>/<beamline>/<btr>/<sample>/<scan>/ff/` |
| `output_dir` | Where the frame caches go (usually the experiment's `reduced_data`) |
| `threshold` | Dark-subtracted intensities at or below this are dropped (a whole number). About 100 works for multiruby; lower, e.g. 25, keeps weak spots |
| `start_frame` | Index of the first data frame in the raw files (0 since 2025-1) |
| `n_workers` | Frame-cache writer threads; `null` uses the slots the job was given |
| `panel_opts` | Gap row/column and flip of each panel, in hexrd's format. The template's values are hexrd's defaults, also used if `panel_opts` is left out |

## 2. Running scans by hand

To make frame caches for scans that were not processed automatically, or to
redo them after changing the config, use `submit_ff_preprocess.py` with a
jobs file. Copy the example next to your data and edit it:

    cp fast-python-scripts/data_analysis/hedm/ff/example_jobs.yaml ff_preprocess_jobs.yaml

The main settings in the jobs file:

| Setting | Meaning |
|---|---|
| `preprocess_config` | Your `dex_ff_config.yaml` (step 1) |
| `spec_scans` | Scans to run: a `spec_file` and its `scans`, e.g. `[10, 12-15]` or `'10-15'`. Several spec files can be listed |
| `par_files` | Instead of (or as well as) `spec_scans`: every scan listed in each `.par` file. The `spec.log` next to the par file is used |
| `qsub` | `true`: one job per scan on the compute farm (needs a machine where `qsub` works); `false`: run here, one scan after the other |
| `qsub_queue`, `qsub_slots` | SGE queue (default `all.q`) and slots per job (default 8) |

Leave out `spec_scans` or `par_files` if you don't use it. Then check the
scans first with a dry run, which prints what would run without running it:

    /nfs/chess/sw/miniforge3_fast/envs/analysis/bin/python \
        fast-python-scripts/data_analysis/hedm/ff/submit_ff_preprocess.py \
        ff_preprocess_jobs.yaml --dry-run

and then run it without `--dry-run`.

**Omega and frames** are read from the scan in `spec.log`. The `ome` counter
holds one value per frame, at the centre of the frame, so with `d` the
average step the scan covers `ome[0] - d/2` to `ome[-1] + d/2`. Before
anything runs, every scan is checked: that range must span exactly as many
frames as the scan has, and each Dexela file must hold that many frames. If
any scan fails a check, nothing runs and the problem scans are listed.

## Output and job logs

One frame cache per panel and scan:

    <output_dir>/<sample>/<scan>/<sample>_<scan>_ff1_<image_n>-cachefile.npz
    <output_dir>/<sample>/<scan>/<sample>_<scan>_ff2_<image_n>-cachefile.npz

Jobs run as `dex_ff_preprocess_job` (check them with `qstat`). Their logs
are in `job_logs/` in the folder of your `dex_ff_config.yaml`.
