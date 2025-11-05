Data Preparation and Shape Creation
================

Overview
--------

This page describes how to prepare and validate input data used by SlicerSalt.

Instructions
-----------------------

Firstly, for good practice, create separate folders for each shape, as well as separate input/output directories.

.. code-block:: console
   mkdir AmygdalaLeft AmygdalaRight CaudateLeft CaudateRight GPLeft GPRight HippocampusLeft HippocampusRight PutamenLeft PutamenRight ThalamusLeft ThalamusRight
   for d in AmygdalaLeft AmygdalaRight CaudateLeft CaudateRight GPLeft GPRight HippocampusLeft HippocampusRight PutamenLeft PutamenRight ThalamusLeft ThalamusRight; do mkdir -p "$d"/{input/{model,volume,defunct},output}; done

Extracting labels using NIRAL ImageMath (preferred)
--------------------------------------------------

Use the `ImageMath` utility from the NIRAL utilities package as the primary method. ImageMath supports operating directly on `.nrrd` files and provides an explicit label extraction operation. See the project pages for downloads and docs:

- https://www.nitrc.org/projects/niral_utilities/
- https://github.com/NIRALUser/niral_utilities

Match the integer label indices in `docs/_static/Subcorticals.label` with their shape names when extracting. The exact invocation to extract a single label is:

``ImageMath input.nrrd -outfile output.nrrd -extractLabel <number>``

Concrete inline example (ImageMath exact form):

.. code-block:: console

   LABELFILE=docs/_static/Subcorticals.label
   INPUT=YourLabelMap.nrrd
   OUTDIR=output_imagemath
   mkdir -p "$OUTDIR"

   awk '/^[[:space:]]*[0-9]+/ {idx=$1; $1=""; sub(/^ +/, ""); label=substr($0, index($0, "\"")); gsub(/\"/,"",label); gsub(/[[:space:]]/,"_",label); print idx, label}' "$LABELFILE" \
   | while read idx label; do
     echo "Extracting label $idx -> $label"
     ImageMath "$INPUT" -outfile "$OUTDIR/${label}.nrrd" -extractLabel "$idx"
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