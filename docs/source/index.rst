Subcortical SlicerSalt Analysis
=========================

Welcome to the SlicerSalt documentation. This documentation provides a quick
start and examples for the main workflows: preparing data, creating shapes,
and populating those shapes.

Use the sections below to quickly navigate to the task you need.

Contents
--------

.. toctree::
   :maxdepth: 2
   :caption: Quick start

   slicersalt_setup
   data_preparation
   shape_creation
   shape_population

Notes
-----
Along the way, we recommend taking note of failed .nrrd or .vtk files, as we may need these files for correction later. We recommend writing down in the format of:

   [SubjectID] vXX: flip along (xyz)

For our data of about 150 subjects, we had an average of 4-5 files per shape that needed corrections.