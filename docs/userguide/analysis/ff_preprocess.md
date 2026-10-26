# Far-field frame caches (Dexela)

Far-field HEDM scans on the Dexela (ff1 + ff2) are turned into hexrd frame
caches: for each panel, the median of the first 100 frames is subtracted as
the dark, the panel gaps and flips are applied, and only pixels above a
threshold are kept. This runs automatically on the compute farm after each
scan taken in ff mode with the Dexela, as set in your experiment's
`analysis_from_spec.yaml` (see below). Scans covering 90° of omega or less
(`min_omega_range`) are skipped. Scans can also be (re)run by hand.

## 1. Get the config file

Everything is set in one YAML config. Ask your beamline scientist for an
example ff config, save it in your experiment's `reduced_data` folder and
edit it:

    /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data/analysis_from_spec.yaml

The processing after each scan always uses the config named
`analysis_from_spec.yaml` in `reduced_data`: every scan processed
automatically uses this file as it is at the time of the scan, so changes to
it apply to all the following scans. For runs by hand, use a config with
another name, e.g. `analyze_ff.yaml` (a copy of `analysis_from_spec.yaml`, or
one with another threshold). A config is either for ff or for powder
(`technique`), not both.

The main settings (all of them are explained in
`/nfs/chess/user/chess_id3a/fast-scripts/data_analysis/README.md`):

| Setting | Meaning |
|---|---|
| `technique` | `ff` |
| `hexrd_env` | Conda env with hexrd that runs the jobs |
| `queue`, `slots` | Queue (`chess_fast.q`) and slots per job |
| `cycle`, `beamline`, `btr` | Where the raw data are: `/nfs/chess/raw/<cycle>/<beamline>/<btr>/<sample>/<scan>/ff/` |
| `output_dir` | Where the frame caches go; `null` for the experiment's `reduced_data` |
| `min_omega_range` | Scans covering this many degrees of omega or less are skipped |
| `threshold` | Dark-subtracted intensities at or below this are dropped (a whole number). About 100 works for multiruby; lower, e.g. 25, keeps weak spots |
| `start_frame` | Index of the first data frame in the raw files (0 since 2025-1) |
| `panel_opts` | Gap row/column and flip of each panel, in hexrd's format. The template's values are hexrd's defaults, also used if `panel_opts` is left out |

## 2. Running scans by hand

To make frame caches for scans that were not processed automatically, or to
redo them after changing the config, use the `fast_analysis` command, on a
machine where `qsub` works. Set it up once by adding this line to your
`~/.bashrc` (it runs `fast_analysis.py` with the `analysis` env's python),
then open a new terminal:

    alias fast_analysis='/nfs/chess/sw/miniforge3_fast/envs/analysis/bin/python /nfs/chess/user/chess_id3a/fast-scripts/data_analysis/fast_analysis.py'

Give it the sample, the scans and the config:

    cd /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data
    fast_analysis <sample> 10,12-15 analyze_ff.yaml --dry-run

or, for every scan in a `.par` file (the sample is the par file's folder):

    fast_analysis --par /nfs/chess/raw/<cycle>/id3a/<btr>/<sample>/<file>.par analyze_ff.yaml --dry-run

`--dry-run` checks the scans and prints the jobs without submitting them;
run it again without `--dry-run` to submit one job per scan. To run the scans
on the machine you are on instead of the queue, one after the other, add
`--local` (it then also works where `qsub` doesn't).

**Omega and frames** are read from the scan in `spec.log`. The `ome` counter
holds one value per frame, at the centre of the frame, so with `d` the
average step the scan covers `ome[0] - d/2` to `ome[-1] + d/2`. Before
anything is submitted, every scan is checked: that range must span exactly
as many frames as the scan has, and each Dexela file must hold that many
frames. If any scan fails a check, nothing is submitted and the problem
scans are listed.

## Output and job logs

One frame cache per panel and scan:

    <output_dir>/<sample>/<scan>/<sample>_<scan>_ff1_<image_n>-cachefile.npz
    <output_dir>/<sample>/<scan>/<sample>_<scan>_ff2_<image_n>-cachefile.npz

Jobs run as `ff_<sample>_<scan>` (check them with `qstat`). Their logs are
in `<output_dir>/job_logs_<user>/`, where `<user>` is your login; logs of the
jobs submitted automatically after each scan are in the folder of the login
spec runs as.
