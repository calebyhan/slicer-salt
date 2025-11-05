Data Preparation
================

Overview
--------

This page describes how to prepare input data used by SlicerSalt.

Instructions
-----------------------

Firstly, organize your input and output directories. For this documentation the expected
input is a folder named `Data/` containing per-subject labelmaps in `.nrrd` format. The
extracted per-shape outputs will be placed under `ShapeData/<ShapeName>/` (for example
`ShapeData/AmygdalaLeft`). Create the expected folders with a command like:

.. code-block:: console

  mkdir -p Data
  mkdir -p ShapeData/{AmygdalaLeft,AmygdalaRight,CaudateLeft,CaudateRight,GPLeft,GPRight,HippocampusLeft,HippocampusRight,PutamenLeft,PutamenRight,ThalamusLeft,ThalamusRight}
  for s in AmygdalaLeft AmygdalaRight CaudateLeft CaudateRight GPLeft GPRight HippocampusLeft HippocampusRight PutamenLeft PutamenRight ThalamusLeft ThalamusRight; do
    mkdir -p "ShapeData/$s/input/model" "ShapeData/$s/input/volume" "ShapeData/$s/output"
  done


Extracting labels using NIRAL ImageMath
--------------------------------------------------

Use the `ImageMath` utility from the NIRAL utilities package as the primary method. ImageMath supports operating directly on `.nrrd` files and provides an explicit label extraction operation. See the project pages for downloads and docs:

- https://www.nitrc.org/projects/niral_utilities/
- https://github.com/NIRALUser/niral_utilities

Match the integer label indices in `docs/_static/Subcorticals.label` with their shape names when extracting. The exact invocation to extract a single label is:

``ImageMath input.nrrd -outfile output.nrrd -extractLabel <number>``

Concrete inline example (process a Data/ folder and write into ShapeData/ folders):

This example loops over all `.nrrd` files in `Data/` and, for each file, extracts every
label defined in `docs/_static/Subcorticals.label` into a per-shape folder under
`ShapeData/`. Output files are named <inputbasename>_<ShapeName>.nrrd so you can track
which subject they came from.

.. code-block:: console

  LABELFILE=Subcorticals.label
  INPUT_DIR=Data
  ROOT=ShapeData

  for f in "$INPUT_DIR"/*.nrrd; do
    [ -e "$f" ] || continue
    base=$(basename "$f" .nrrd)

    # Parse label file → (idx, label)
    awk '/^[[:space:]]*[0-9]+/ {
        idx=$1; $1="";
        sub(/^ +/, "");
        lbl=substr($0, index($0, "\""));     # from first quote to end
        gsub(/\"/, "", lbl);                 # drop quotes
        gsub(/[[:space:]]/, "_", lbl);       # spaces → underscores (safe)
        print idx, lbl
    }' "$LABELFILE" | while read -r idx label; do

      # Ensure per-shape dirs exist
      mkdir -p "$ROOT/$label/input/model" "$ROOT/$label/input/volume" "$ROOT/$label/output"

      out="$ROOT/$label/input/volume/${base}_${label}.nrrd"
      echo "Extracting $f -> $out (label $idx)"
      ImageMath "$f" -outfile "$out" -extractLabel "$idx"
    done
  done

Notes:

- Some ImageMath builds require a leading dimension argument (e.g. `3`). In that case place the dimension before the other arguments, for example:

  ``ImageMath 3 input.nrrd -outfile output.nrrd -extractLabel <number>``

- `docs/_static/Subcorticals.label` contains the mapping of indices to shape names; the script above uses those names (spaces replaced with underscores) for output filenames.

Other available tools
---------------------

Other tools can also split labelmaps (for example FSL's `fslmaths`, `unu` from Teem, or a SimpleITK/ITK-based script). We mention them here as alternatives, but we do not provide usage examples for them in this documentation. Choose whichever tool fits your environment and workflow.

Next steps
----------

After preparing data, follow the Shape Creation guide to create shapes using the SPHARM-PDM generator.