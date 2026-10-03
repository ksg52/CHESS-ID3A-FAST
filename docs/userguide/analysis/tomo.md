# Tomography reconstruction

Tomography scans are reconstructed with a Jupyter notebook that uses tomopy.

## Files

The notebook comes with the `fast-python-scripts` folder, which is already
set up in your experiment's `reduced_data` folder. Check that it is there:

    ls /nfs/chess/aux/cycles/<cycle>/id3a/<btr>/reduced_data/fast-python-scripts

In `fast-python-scripts/data_analysis/tomo/`:

| File | What it is |
|---|---|
| `tomo_recon_notebook.ipynb` | The reconstruction notebook |
| `TomoFunctions/tomo_lib.py`, `TomoFunctions/tomoFunctions3.py` | Helper functions the notebook imports |

## Python environment

Use the conda env `tomo`:

    /nfs/chess/sw/miniforge3_fast/envs/tomo

In VS Code, open the notebook, then:

1. Click **Select Kernel** (top right).
2. Choose **Python Environments...**
3. Pick `tomo` (`/nfs/chess/sw/miniforge3_fast/envs/tomo/bin/python`). If it is not listed, choose **Enter interpreter path** and paste that path.

## Inputs

All inputs are in the cells at the top of the notebook. The cell that builds
`cfg` (it starts with *"Build cfg from the inputs above"*) has no inputs; do
not edit it.

**Experiment and scans** (first input cell):

* `raw_dir`, `aux_dir`, `cycle`, `beamline`, `btr`: where the raw data are
  (`<raw_dir>/<cycle>/<beamline>/<btr>`) and where results go
  (`<aux_dir>/<cycle>/<beamline>/<btr>/reduced_data`).
* `tomo_detector` and `detectors`: which detector's files to read and how.
* `scan_list`: one entry per tomography scan:

    | Position | Meaning |
    |---|---|
    | 1 | sample folder name |
    | 2 | spec file name, e.g. `spec.log` |
    | 3 | tomography scan number |
    | 4 | `[first, last]` rows to reconstruct |
    | 5 | `[first, last]` centering rows |
    | 6 | rotation centers found for the two centering rows |
    | 7 | `x_bounds`: columns to keep when saving the reconstruction |
    | 8 | `y_bounds`: rows to keep when saving the reconstruction |

* `dark_list`, `bright_list`: the dark-field and bright-field scan for each
  entry of `scan_list`, as `[sample folder, spec file, scan number]`.

**Which scan** (`scan_index`): the position in `scan_list` to process,
starting at 0.

**Processing settings** (the cell starting with *"Processing settings"*):

| Setting | Meaning |
|---|---|
| `export_plots` | Save the plots into the output folder |
| `dateID` | Added to the output folder name |
| `theta_label`, `presample_intensity_label` | spec counter names for the rotation angle and the incident intensity (e.g. `ic3`) |
| `roi_col_start`, `roi_col_end` | Detector columns to process |
| `post_recon_remove_ring`, `remove_ring_rwidth` | Ring removal after reconstruction |
| `post_recon_gauss_filter`, `gauss_filter_width` | Gaussian filter after reconstruction |
| `save_sinograms` | Save the corrected sinograms (`.h5`) |
| `log_times` | Print how long each step takes |

Results go to
`<aux_dir>/<cycle>/<beamline>/<btr>/reduced_data/<sampleID>_<dateID>/`.

## Workflow

1. **Set the inputs** and run the cells down to and including the cell that
   builds `cfg`. Run the next cell to save the settings as a YAML file in the
   output folder.
2. **Sequence 1: find the rotation center.** Reconstructs only the two
   centering rows for a range of trial centers and plots them. Pick the best
   center for each row, enter the two values as position 6 of the scan's
   `scan_list` entry, and repeat until you are happy with them.
3. **Sequence 2: full reconstruction.** Processes all rows between the
   first and last rows (position 4), using centers interpolated between the
   two centering rows. Plot the first and last slice and set `x_bounds` /
   `y_bounds` to crop the volume.
4. **Save** the cropped reconstruction as a stack of TIFFs (float16 or
   float32), a single TIFF stack, and/or an `.h5` file.
