Scilpy visualization module
===========================

One big module of the Scilpy library that has to abide its structure **to the letter** is the *visualization* module. The thing is that in visualization, it is mostly impossible to use a single dependency to do all the work. Thus, the module is split by dependency (`fury`, `PIL`, `VTK` and `matplotlib`), which draw clear lines in the code :

- `Fury` is the backend linking common data formats in DWI to rendering utilities (online or offline). It takes all those formats and renders them using the associated primitives. It binds them in `VTK` to render and create the interactors and windows.

- `PIL` is the backend used to compose all screenshots realized using the module. It takes a series of images and transparencies and layers them in a single screenshot (for example an anatomical image with a wm mask contour overlay and peaks from DTI fitting). It also handles composing multiple screenshots into mosaics, overlapping or not.

- `VTK` is the backend managing redering primitives. Most of its functionalities are wrapped in `Fury`. We use it mostly to handle colors, lookup tables and functions not available in `Fury` yet.

- `matplotlib` is the backend used to create plots and handle lookup tables. 2D data does not require `Fury` nor `VTK`; using matplotlib is faster. In addition, it provides an extensive collection of lookup tables, moreso than `VTK`.

**Structure of the visualization module**

.. image:: /images/visualization_umlish.png
   :align: center

The vizualisation module is plis in 4 components : slice, screenshot, plot and color. They all have integral roles to play in the visualization of DWI data.

*From raw data to the screen*

The first step is to take the data encoded in a native DWI format and convert it to primitives in a rendering window. This is done using the **slice.py** module. Assuming no interaction and offline rendering, functions from this modules will decompose the input data into multiple snapshots, using either the data's discretization or one provided by the user. **All functions in this module must return generators, to compute on the fly and save memory space.**

*From the screen to screenshots*

Notice the use of words until now, we have gathered **snapshots**. Now is the time to turn them into real **screenshots**. To do so, we compose multiple *snapshots* of the same data, on layers and using transparency, into one single *screenshot* to save to the disk. This is done using the **screenshot.py** module. Functions in this module are designed to take *snapshots* of layers and compose them together. It can also process multiple *snapshots* and/or *screenshots* into mosaics. **All functions in this module must return generators, to compute on the fly and save memory space.**

*Color me snapshots*

The *screenshot* module is great, but it is limited in its use of colors. The thing is **Fury** does great, but not all what we want (not the way we want it, let's say). Thus, we use **VTK** and **matplotlib** to handle colors and lookup tables. This is done using the **color.py** module. Functions in this module are all designed to output a list of RGBa colors, a *vtkLookupTable* or a *matlplotlib* lookup table function. They must be used in all vizualisation sub-modules, in order for the colors to be perfect for Scilpy.

*Plotting*

Plotting is a work-in-progress. As of now, only **chord charts** uses matplotlib. We created basic handles for this, but didn't create any modules. Once done, stuff from lecay for chord charts will have to be integratedin it.