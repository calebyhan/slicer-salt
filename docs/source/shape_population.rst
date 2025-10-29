Shape Population
================

Overview
--------

This page describes how to use the Shape Population Viewer module in SlicerSalt to verify the correctness of the SPHARM-PDM outputs.

Instructions
---------------------------

First we need to prepare the outputs from the `SPHARM-PDM Generator`` module. Once you have the output files, you can use the Shape Population Viewer to visualize and validate the populated shapes.
However, simply loading the files would take a while, so we can divide up the `*SPHARM.vtk` files into smaller chunks.

First ensure that we are in the `Shape/output` directory.

.. code-block:: console

   mkdir -p SP1 SP2 SP3 && i=0; for f in Step3_ParaToSPHARMMesh/*SPHARM.vtk; do ((i++)); n=$(( (i-1)%3 + 1 )); cp "$f" SP$n/; done

Verification
---------------------

In SlicerSalt, select the Shape Creation -> SPHARM-PDM Generator module.

Next steps
----------

