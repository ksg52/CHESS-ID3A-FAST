# Powder diffraction integration

Dexela (ff1 + ff2) powder data are integrated with hexrd into intensity vs.
two-theta lineouts. This runs automatically on the compute farm after each
scan taken in ff mode with the Dexela, as set in your experiment's
`analysis_from_spec.yaml` (see below). Scans can also be (re)run by hand.

## 1. Get the config file

Everything is set in one YAML config. Ask your beamline scientist for an
example powder config, save it in your experiment's `reduced_data` folder
and edit it:

    /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data/analysis_from_spec.yaml

The integration after each scan always uses the config named
`analysis_from_spec.yaml` in `reduced_data`. For runs by hand any name works.
A config is either for ff or for powder (`technique`), not both.

The main settings (all of them are explained in
`/nfs/chess/user/chess_id3a/fast-python-scripts/data_analysis/README.md`):

| Setting | Meaning |
|---|---|
| `technique` | `powder` |
| `hexrd_env` | Conda env with hexrd that runs the jobs |
| `queue`, `slots` | Queue (`chess_fast.q`) and slots per job |
| `cycle`, `beamline`, `btr` | Where the raw data are: `/nfs/chess/raw/<cycle>/<beamline>/<btr>/<sample>/<scan>/ff/` |
| `output_dir` | Where results go; `null` for the experiment's `reduced_data` |
| `instrument` | Calibrated instrument file (full path): `.yml`, or `.hexrd` / `.h5` as saved by hexrdgui |
| `tth_range` | Two-theta range in degrees, e.g. `[0.1, 24]` |
| `eta_range` | One or more `[eta_min, eta_max]` ranges; one lineout per range |
| `eta_resolution` | Eta bin size in degrees. `0.25` is recommended: `null` (finest resolution) needs about 32 GB per process, `0.25` about 4 GB |
| `average_frames` | `false`: integrate every frame; `true`: integrate the average of the frames |
| `dark_scan_n` | Scan number of the darkfield frames, or `null` for no dark subtraction. This scan itself is not integrated |
| `skip_frames` | Frames to drop at the start of each scan |
| `panels` | Panel names and the flip applied to each (`ff1: lr`, `ff2: ud`) |
| `save_text`, `save_images`, `save_polar` | Save the lineout `.txt`, the lineout plot, and/or the polar image |

## 2. Running scans by hand

To integrate scans that were not integrated automatically, or to redo them
after changing the config, use the `fast_analysis` command, on a machine
where `qsub` works. Set it up once by adding this line to your `~/.bashrc`
(it runs `fast_analysis.py` with the `analysis` env's python), then open a
new terminal:

    alias fast_analysis='/nfs/chess/sw/miniforge3_fast/envs/analysis/bin/python /nfs/chess/user/chess_id3a/fast-python-scripts/data_analysis/fast_analysis.py'

Give it the sample, the scans and the config:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    fast_analysis <sample> 2,4-6 analysis_from_spec.yaml --dry-run

or, for every scan in a `.par` file (the sample is the par file's folder):

    fast_analysis --par /nfs/chess/raw/<cycle>/id3a/<btr>/<sample>/<file>.par analysis_from_spec.yaml --dry-run

`--dry-run` checks the scans and prints the jobs without submitting them;
run it again without `--dry-run` to submit one job per scan. To run the scans
on the machine you are on instead of the queue, one after the other, add
`--local` (it then also works where `qsub` doesn't). Each scan needs
one raw file per panel; if one is missing, nothing is submitted and the
problem scans are listed. The dark scan is skipped.

## Output and job logs

For each frame (or the frame average) and each eta range:

* per frame: `<output_dir>/<sample>/<scan>/<sample>_<scan>_<image_n>_frame<NNNN>_eta<eta_min>_<eta_max>.txt`
* averaged: `<output_dir>/<sample>/<sample>_<scan>_<image_n>_eta<eta_min>_<eta_max>.txt`

with `_lineout.png` and `_polar.png` plots next to them. Negative eta bounds
are written as e.g. `minus180`.

Jobs run as `powder_<sample>_<scan>` (check them with `qstat`). Their logs
are in `<output_dir>/job_logs/`.
