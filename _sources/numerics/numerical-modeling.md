# Principles

Numerical models in water resources engineering approximate the motion of fluids through iterative solutions of the {term}`Navier-Stokes Equation`. The role of numerical models is becoming more and more important where models are distinguished regarding simplification hypotheses (e.g., for dimensions or fluid characteristics). Purely hydrodynamic models simulate the motion of water and  have high accuracy for simulating flow phenomena, but major challenges remain for morphodynamic modeling. While one-dimensional (**1d** cross-section-averaged) modeling is slowly abandoned for its incapacity to account for complex flow phenomena in natural rivers, two-dimensional (**2d**) and three-dimensional (**3d**) models are becoming more and more popular. Still, there are challenges in model choices and understanding numerical models. In this context, {cite:t}`mosselman_five_2016` highlight five widespread and common problems in the creation and interpretation of numerical models. These five mistakes are:

1. Preparation: One-dimensional (1d), two-dimensional (2d), and three-dimensional (3d) models require similar input data (flow series, stage-discharge relation, roughness, digital elevation model, grain sizes). What varies are the computation (3d > 2d > 1d) and the calibration (1d > 2d > 3d) efforts.
2. Grid setup: The model boundaries need to be at an adequate distance to the area of interest. An inflow boundary should only be along the permanently wetted riverbed and the most upstream 1-2% of the modeled channel bed should have a non-erosive constraint assigned to the cells. Otherwise, the model may be unstable because of locally very high velocity and erosion rates close to the inflow boundary.
3. Model setup: Read and understand how turbulence closures are implemented in the model to set the model parameters used for the turbulence closure realistically and yield a stable model.
4. Model validation/post-processing: Wrong confidence in poorly validated numerical models: Every model requires validation data, which involves exhausting and labor-intensive fieldwork.
5. Model interpretation: The direction of sediment transport and water flow vectors mostly differ.

This chapter introduces open-access and open-source software with extensive tutorials on pre-processing (geo) spatially explicit data, setting up model control files, running models, and post-processing. Tutorials are available for the following software:

* **BASEMENT (open-access)**<br>The {ref}`chpt-basement` tutorial introduces 2d hydrodynamic modelling with the ETH Zurich's (Switzerland) numerical model *BASEMENT* 3.x, which was primarily developed with benchmark tests on **mountain rivers/streams**.
* **TELEMAC (open source)**<br>Open TELEMAC-MASCARET is a powerful software suite for a large variety of **rivers, lakes, and even ocean deltas**.
  * Get an overview of files and model options in the {ref}`TELEMAC introduction <chpt-telemac>` section.
  * The {ref}`chpt-telemac2d` tutorial introduces 2d hydrodynamic modelling with standard *SLF* (selafin) geometry files.
  * The {ref}`chpt-telemac3d-med` tutorial introduces 3d hydrodynamic modeling based on the highly efficient *MED* file library and using the {ref}`salome-hydro` software suite.
<!--
* OpenFOAM8
-->