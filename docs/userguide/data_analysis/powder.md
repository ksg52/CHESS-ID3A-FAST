# Powder diffraction integration

Dexela (ff1 + ff2) powder data are integrated with hexrd into intensity vs.
two-theta lineouts. This runs automatically on the compute farm after each
scan, and whole sample folders can be (re)submitted by hand.

The scripts come with the `fast-python-scripts` repository. Clone it into
your experiment's `reduced_data` folder:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    git clone https://gitlab01.classe.cornell.edu/ad785/fast-python-scripts.git

## 1. Make the config file

Each experiment needs one YAML config in its `reduced_data` folder. Copy the
template from your clone and edit it:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    cp fast-python-scripts/postscan_automation/powder/dex/config.yaml dex_powder_int_config.yaml

The template comments explain every setting; the main ones are:

| Setting | Meaning |
|---|---|
| `cycle`, `beamline`, `btr` | Where the raw data are: `/nfs/chess/raw/<cycle>/<beamline>/<btr>/<sample>/<scan>/ff/` |
| `instrument` | Calibrated instrument file: `.yml`, or `.hexrd` / `.h5` as saved by hexrdgui |
| `output_dir` | Where results go; leave out or `null` for `/nfs/chess/aux/cycles/<cycle>/<beamline>/<btr>/reduced_data` |
| `tth_range` | Two-theta range in degrees, e.g. `[0.1, 24]` |
| `eta_range` | One or more `[eta_min, eta_max]` ranges; one lineout per range |
| `eta_resolution` | Eta bin size in degrees. `0.25` is recommended: `null` (finest resolution) needs about 32 GB per process, `0.25` about 4 GB |
| `average_frames` | `false`: integrate every frame; `true`: integrate the average of the frames |
| `dark_scan_n` | Scan number of the darkfield frames, or `null` for no dark subtraction |
| `skip_frames` | Frames to drop at the start of each scan |
| `panels` | Panel names and the flip applied to each (`ff1: lr`, `ff2: ud`) |
| `save_text`, `save_images`, `save_polar` | Save the lineout `.txt`, the lineout plot, and/or the polar image |

## 2. Automatic integration after each scan

Turn the postscan integration on in spec:

    powder_postscan_on

After every scan with the Dexela as far-field detector (`SYNC_FF_DETECTOR`
is `"dexela"`), spec submits one integration job for that scan. If the
config file is missing, the scan is skipped with a message. Turn it off with

    powder_postscan_off

## 3. Submitting whole sample folders

To integrate scans that were collected with the postscan integration off, or
to redo them after changing the config, submit whole sample folders with
`submit_powder_folders.py` from your clone. Run it with the `station_env`
env, on a machine where `qsub` works:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    /nfs/chess/sw/miniforge3_fast/envs/station_env/bin/python \
        fast-python-scripts/data_analysis/powder/submit_powder_folders.py \
        /nfs/chess/raw/<cycle>/id3a/<btr>/<sample> [more sample folders] [options]

It submits one job per scan in each sample folder, exactly as spec does,
using the same `dex_powder_int_config.yaml`. It skips the dark scan and
scans that don't have a raw file for each panel, and it stops before
submitting anything if a folder doesn't match the config's `cycle`,
`beamline` and `btr`.

Folder names relative to the current folder work too, e.g. from
`/nfs/chess/raw/<cycle>/id3a/<btr>`: `... submit_powder_folders.py sample1 sample2`.

| Option | Meaning |
|---|---|
| `--scans 2,4-6` | Only these scan numbers |
| `--dry-run` | Print the `qsub` commands without submitting them |
| `-c <file>` | Use another config file |

## Output and job logs

For each frame (or the frame average) and each eta range:

* per frame: `<output_dir>/<sample>/<scan>/<sample>_<scan>_<image_n>_frame<NNNN>_eta<eta_min>_<eta_max>.txt`
* averaged: `<output_dir>/<sample>/<sample>_<scan>_<image_n>_eta<eta_min>_<eta_max>.txt`

with `_lineout.png` and `_polar.png` plots next to them. Negative eta bounds
are written as e.g. `minus180`.

Jobs run as `dex_powder_integration_job` (check them with `qstat`). Their
logs are in `<aux btr>/reduced_data/job_logs/`.
