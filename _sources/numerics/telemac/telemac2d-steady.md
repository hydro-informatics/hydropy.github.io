(telemac2d-steady)=
# Steady 2d

```{admonition} Requirements
This tutorial is designed for **advanced beginners** and before diving into this tutorial make sure to complete the {ref}`TELEMAC pre-processing tutorial <slf-prepro-tm>`.

The case featured in this tutorial was established with the following software:
* {ref}`Notepad++ <npp>` text editor (any other text editor will do just as well.)
* TELEMAC v8p2r0 or newer ({ref}`stand-alone installation <modular-install>`).
* {ref}`QGIS <qgis-install>` and the {ref}`PostTelemac plugin <tm-qgis-plugins>`.
* Debian Linux 10 (Buster) / Debian 11 installed on a Virtual Machine (read more in the {ref}`software chapter <chpt-vm-linux>`).
```

## Get Started

This section builds on the SELAFIN (`*.slf`) geometry and the Conlim (`*.cli`) boundary condition files that result from the {ref}`TELEMAC pre-processing tutorial <slf-prepro-tm>`. Both files can also be downloaded from the supplemental materials repository of this eBook:

* [Download qgismesh.slf](https://github.com/hydro-informatics/telemac/raw/main/bk-slf/qgismesh.slf) (uses **EPSG:32633** - ETRS 89 / UTM zone 33N).
* [Download boundaries.cli](https://github.com/hydro-informatics/telemac/raw/main/bk-slf/boundaries.cli).

Consider saving both files in a new folder, such as `/steady2d-tutorial/` that will contain all model files.

```{admonition} Download simulation files
All simulation files used in this tutorial are available at [https://github.com/hydro-informatics/telemac/tree/main/steady2d-tutorial/](https://github.com/hydro-informatics/telemac/tree/main/steady2d-tutorial/).
```

## Steering File (CAS)

The steering file has the file ending `*.cas` (presumably derived from the French word *cas*, which means *case* in English). The `*.cas` file is the main simulation file with information about references to the two always mandatory files (i.e., the [SELAFIN](https://gdal.org/drivers/vector/selafin.html) `*.slf` geometry and the `*.cli` boundary files) and optional files, as well as definitions of simulation parameters. The steering file can be created or edited with a basic text editor or advanced GUI software such as {ref}`Fudaa PrePro <fudaa>` or {ref}`BlueKenue <bluekenue>`. This tutorial uses a basic text editor (e.g., {ref}`Notepad++ <npp>` on Windows).

```{admonition} Fudaa PrePro
*Fudaa PrePro* comes with variable descriptions that facilitate the definition of boundaries, initial conditions, and numerical parameters in the steering file. However, Fudaa PrePro makes file directions according to the platform on which it is running (i.e., `\` on Windows and `/` on Linux) and writes absolute file paths to the `*.cas` file, which often requires manual correction (e.g., if Fudaa PrePro is used for setting up a `*.cas` file on Windows for running a TELEMAC simulation on Linux). For working with Fudaa PrePro, follow the download instructions in the {ref}`software chapter <fudaa>`. To launch Fudaa Prepro, open *Terminal* (Linux) or *Command Prompt* (Windows) and tap:

* `cd` to the installation (download) directory of Fudaa PrePro
* Start the GUI (requires java):
  * *Linux*: `sh supervisor.sh`
  * *Windows*: `supervisor.bat`
```

For this tutorial, **create a new text file** in the same folder where `qgismesh.slf` and `boundaries.cli` live, and name it, for instance, `steady2d.cas` (e.g., `/steady2d-tutorial/steady2d.cas`). The next sections guide through parameter definitions that stem from the {{ tm2d }}. The final steering file can be downloaded from the supplemental materials repository ([download steady2d.cas](https://github.com/hydro-informatics/telemac/raw/main/steady2d-tutorial/steady2d.cas)).

### Overview of the CAS File

The below box shows the provided [steady2d.cas](https://github.com/hydro-informatics/telemac/raw/main/steady2d-tutorial/steady2d.cas) file that can be used for running this tutorial.

````{admonition} Expand to view the complete .CAS file
:class: note, dropdown

```fortran
/---------------------------------------------------------------------
/ TELEMAC2D Version v8p4
/ STEADY HYDRODYNAMICS TRAINING
/---------------------------------------------------------------------

/ steady2d.cas
/------------------------------------------------------------------/
/			COMPUTATION ENVIRONMENT
/------------------------------------------------------------------/
TITLE : '2d steady'
/
BOUNDARY CONDITIONS FILE : boundaries.cli
GEOMETRY FILE            : qgismesh.slf
RESULTS FILE           : r2dsteady.slf
/
MASS-BALANCE : YES / activates mass balance printouts - does not enforce mass balance
VARIABLES FOR GRAPHIC PRINTOUTS : U,V,B,H,S,Q,F / Q enables boundary flux equilibrium controls, B required for gaia (optional)
/
/------------------------------------------------------------------/
/			GENERAL PARAMETERS
/------------------------------------------------------------------/
TIME STEP : 1.
NUMBER OF TIME STEPS : 15000
GRAPHIC PRINTOUT PERIOD : 200
LISTING PRINTOUT PERIOD : 100
/
/------------------------------------------------------------------/
/			NUMERICAL PARAMETERS
/------------------------------------------------------------------/
/ General solver parameters
DISCRETIZATIONS IN SPACE : 11;11
FREE SURFACE GRADIENT COMPATIBILITY : 0.1  / default 1.
ADVECTION : YES
/
/ STABILITY CONTROLS
PRINTING CUMULATED FLOWRATES : YES
/
/ FINITE ELEMENT SCHEME PARAMETERS
/------------------------------------------------------------------
TREATMENT OF THE LINEAR SYSTEM : 2 / default is 2 - use 1 to avoid smoothened results
SCHEME FOR ADVECTION OF VELOCITIES : 14 / alternatively keep 1
SCHEME FOR ADVECTION OF TRACERS : 5
SCHEME FOR ADVECTION OF K-EPSILON : 14
IMPLICITATION FOR DEPTH : 0.55 / should be between 0.55 and 0.6
IMPLICITATION FOR VELOCITY : 0.55 / should be between 0.55 and 0.6
IMPLICITATION FOR DIFFUSION OF VELOCITY : 1. / v8p4 default
IMPLICITATION COEFFICIENT OF TRACERS : 0.6 / v8p4 default
MASS-LUMPING ON H : 1.
MASS-LUMPING ON VELOCITY : 1.
MASS-LUMPING ON TRACERS : 1.
SUPG OPTION : 0;0;2;2 / classic supg for U and V
/
/ SOLVER
/------------------------------------------------------------------
INFORMATION ABOUT SOLVER : YES
SOLVER : 1
MAXIMUM NUMBER OF ITERATIONS FOR SOLVER : 200 / maximum number of iterations when solving the propagation step
MAXIMUM NUMBER OF ITERATIONS FOR DIFFUSION OF TRACERS : 60 / tracer diffusion
MAXIMUM NUMBER OF ITERATIONS FOR K AND EPSILON : 50 / diffusion and source terms of k-e
/
/ TIDAL FLATS
TIDAL FLATS : YES
CONTINUITY CORRECTION : YES / default is NO
OPTION FOR THE TREATMENT OF TIDAL FLATS : 1
TREATMENT OF NEGATIVE DEPTHS : 2 / value 2 or 3 is required with tidal flats - default is 1
/
/ MATRIX HANDLING
MATRIX STORAGE : 3 / default is 3
/
/ BOUNDARY CONDITIONS
/------------------------------------------------------------------
/
/ Liquid boundaries
PRESCRIBED FLOWRATES  : 35.; 0.
PRESCRIBED ELEVATIONS : 374.80565;371.33
/
/ Type of velocity profile can be 1-constant normal profile (default) 2-UBOR and VBOR in the boundary conditions file (cli) 3-vector in UBOR in the boundary conditions file (cli) 4-vector is proportional to the root (water depth, only for Q) 5-vector is proportional to the root (virtual water depth), the virtual water depth is obtained from a lower point at the boundary condition (only for Q)
VELOCITY PROFILES : 4;1
/
/ Friction at the bed
LAW OF BOTTOM FRICTION : 4 / 4-Manning
FRICTION COEFFICIENT : 0.03 / Roughness coefficient
/ Friction at the boundaries
LAW OF FRICTION ON LATERAL BOUNDARIES : 4 / 4-Manning
ROUGHNESS COEFFICIENT OF BOUNDARIES : 0.03 / Roughness coefficient
/
/ INITIAL CONDITIONS
/ ------------------------------------------------------------------
INITIAL CONDITIONS : 'ZERO DEPTH' / start with dry model conditions
/
/-------------------------------------------------------------------
/			TURBULENCE
/-------------------------------------------------------------------
/
DIFFUSION OF VELOCITY : YES / default is YES
TURBULENCE MODEL : 3
/
&ETA
```

```{admonition} What means &ETA?
:class: note
The `&ETA` keyword at the bottom of the `*.cas` template file makes TELEMAC printing out keywords and the values assigned to them when it runs its *Damocles* algorithm.
```
````

(tm2d-gen)=
### General Parameters

The general parameters define the computation environment starting with a simulation title and the most important links to the two mandatory input files:

* `BOUNDARY CONDITIONS FILE : boundaries.cli` - with a *MED* file, use a *BND* boundary file
* `GEOMETRY FILE : qgismesh.slf`

The model **output** can be defined with the following keywords:

* `RESULTS FILE : r2dsteady.slf` - can be either a *MED* file or an *SLF* file
* `VARIABLES FOR GRAPHIC PRINTOUTS` (i.e., output parameters):  `U,V,H,S,Q,F` - many more options can be found in section 1.317 (page 85) of the {{ tm2dref }}.

The velocities (`U` and `V`), the water depth (`H`), and the discharge (`Q`) are standard variables that should be used in every simulation. In particular, the discharge `Q` is required to check when (steady) s converge at the inflow and outflow boundaries. Moreover, discharge `Q` enables to trace integrated fluxes along any user-defined line in the model. The procedure for verifying and identify discharges is described in the {ref}`discharge verification <verify-steady-tm2d>` section in the post-processing.

The time variables (`TIME STEP` and `NUMBER OF TIME STEPS`) define the simulation length. The printout periods (`GRAPHIC PRINTOUT PERIOD` and `LISTING PRINTOUT PERIOD`) define the result output frequency. The **smaller the printout period**, **the longer will take the simulation** because writing results is a time-consuming process. The printout periods (frequencies) refer to a multiple of the `TIME STEPS` parameter and need to be a smaller number than the `NUMBER OF TIME STEPS`. Read more about timestep parameters in the {{ tm2d }} in sections 5 and 12.4.2.

In addition, the `MASS-BALANCE : YES` setting will print out mass fluxes and errors in the computation region, which is an important parameter for verifying the plausibility of the model. Note that this keyword only enables mass balance printouts and does not enforce mass balance of the model, which must be achieved through a consistent model setup following this tutorial and the {{ tm2d }}.

````{admonition} Expand to review the GENERAL PARAMETERS used in this tutorial
:class: note, dropdown
```fortran
TITLE : '2d steady flow'
/
BOUNDARY CONDITIONS FILE : boundaries.cli
GEOMETRY FILE : qgismesh.slf
RESULTS FILE : r2dsteady.slf
/
MASS-BALANCE : YES / activates mass balance printouts - does not enforce mass balance
VARIABLES FOR GRAPHIC PRINTOUTS : U,V,H,S,Q,F / Q enables boundary flux equilibrium controls
/
TIME STEP : 1.
NUMBER OF TIME STEPS : 15000
GRAPHIC PRINTOUT PERIOD : 200
LISTING PRINTOUT PERIOD : 100
```
````

(tm2d-numerical)=
### General Numerical Parameters

**The following descriptions refer to section 7.1 in the {{ tm2d }}.**

Telemac2d comes with three solvers for approximating the depth-averaged {term}`Navier-Stokes equations` (i.e., the {term}`Shallow water equations`) {cite:p}`p. 262 in <kundu_fluid_2008>` that can be chosen by adding the **EQUATIONS** keyword to the `*.cas` file:

* `EQUATIONS : SAINT-VENANT FE` is the **default** that makes Telemac2d use a Saint-Venant finite element method,
* `EQUATIONS : SAINT-VENANT FV` makes Telemac2d use a Saint-Venant finite volume method, and
* `EQUATIONS : BOUSSINESQ` makes Telemac2d use the {term}`Boussinesq approximation`, which assumes constant density (incrompressible fluid assumption) and is not to be confused with the {term}`Boussinesq hypothesis`.

In addition, a type of discretization has to be specified with the **DISCRETIZATIONS IN SPACE** keyword, which is a list of five integer values. The five list elements define spatial discretization schemes for (1) velocity, (2) depth, (3) tracers, (4) $k-\epsilon$ turbulence, and (5) $\tilde{\nu}$ advection (Spalart-Allmaras), respectively. The minimum length of the keyword list is 2 (for velocity and depth) and all other elements are optional. The list elements may take the following values defining spatial discretization:

* `11` (**default**) activates (linear) triangular discretization in space (i.e., 3-node triangles),
* `12` activates quasi-bubble discretization with 4-nodes, and
* `13` activates quadratic discretization with 6-nodes.

The {{ tm2d }} recommend using **the default value of `DISCRETIZATIONS IN SPACE : 11;11`** that assigns a linear discretization for velocity and water depth, which **is computationally fast but potentially unstable**. The option `12;11` may be used to reduce free surface instabilities or oscillations (e.g., along with steep bathymetry gradients). The option `13;11` increases the accuracy of results, the computing time, memory usage, and it is currently not available in Telemac2d.

In addition, the **FREE SURFACE GRADIENT** keyword can be defined for increasing the stability of a model. Its default value is `1.0`, but it can be reduced close to zero to achieve stability. The developers propose a minimum value of `0.`, but more realistic results can be yielded by setting this keyword to slightly more than zero (e.g., `0.1`). For instance, the following keyword combination may reduce surface instabilities (also referred to as *wiggles* or *oscillations*):

```fortran
DISCRETIZATIONS IN SPACE : 12;11
FREE SURFACE GRADIENT : 0.1
```

By default {term}`Advection` is activated through the keyword `ADVECTION : YES` and it can be deactivated for particular terms only:

```fortran
ADVECTION OF H : NO / deactivates depth advection
ADVECTION OF U AND V : NO / deactivates velocity advection
ADVECTION OF K AND EPSILON : NO / deactivates turbulent energy and dissipation (k-e model) or the Spalart-Allmaras advection
ADVECTION OF TRACERS : NO / deactivates tracer advection
```

The **PROPAGATION** keyword (default: `YES`) steers the simulation of propagation and related phenomena. For instance, disabling propagation (`PROPAGATION : NO`) will also disable {term}`Diffusion`. The other way round, when propagation is enabled, {term}`Diffusion` can be disabled separately. Read more about {term}`Diffusion` in Telemac2d in the {ref}`turbulence <tm2d-turbulence>` section.

(tm2d-fe)=
### Finite Elements

**The following descriptions refer to section 7.2.1 in the {{ tm2d }}.**

Telemac2d uses finite elements for iterative solutions to the {term}`Shallow water equations`. The **TREATMENT OF THE LINEAR SYSTEM** keyword enables replacing the original set of equations (option `1`) involved in TELEMAC's finite element solver with a generalized wave equation (**option `2`**). The replacement (i.e., the use of the **generalized wave equation**) is set to **default since v8p2** and decreases computation time, but smoothens the results. This default (`TREATMENT OF THE LINEAR SYSTEM : 2`) automatically activates mass lumping for depth and velocity, and implies explicit velocity diffusion.

```{admonition} Use SCHEME FOR ADVECTION in lieu of TYPE OF ADVECTION
:class: note, dropdown
The **TYPE OF ADVECTION** keyword is a list of four integers that define the advection schemes for (1) velocities (both $u$ and $v$), (2) water depth $h$, (3) tracers, and (4) turbulence ($k-\epsilon$ or $\tilde{\nu}$). The value provided for (2) depth is ignored since v6p0 and a list of two values is sufficient in the absence of (3) tracers and a specific (4) turbulence model. Thus, in lieu of `TYPE OF ADVECTION`, the `SCHEME FOR ADVECTION OF VELOCITIES` keyword should be used. The default is `TYPE OF ADVECTION : 1;5;1;1` (where the `5` for water depth stems from an older Telemac2d version and does not trigger the PSI scheme). However, **the {{ tm2d }} indicate that the TYPE OF ADVECTION keyword will be deprecated in future releases.**
```

The {{ tm2d }} state that the following scalar **SCHEME FOR ADVECTION** keywords apply instead of the soon deprecated TYPE OF ADVECTION list:

```fortran
SCHEME FOR ADVECTION OF VELOCITIES : 1 / default
SCHEME FOR ADVECTION OF TRACERS : 1 / default
SCHEME FOR ADVECTION OF K-EPSILON : 1 / default
```

The three `SCHEME FOR ADVECTION` scalar keywords may take the following values:

* `1` sets a not mass-conservative method of characteristics (default for all),
* `2` sets a semi-implicit scheme and activates the Streamline Upwind Petrov Galerkin (SUPG) scheme (read more below),
* `3`, `4`, `13`, and `14` activate the so-called NERD scheme (these numbers activate different schemes in 3d only),
* `5` sets a mass-conservative PSI distributive scheme (do not use with tidal flats), and
* `15` sets the mass-conservative ERIA scheme that works with tidal flats.

Options `4` and `5` require that the {term}`CFL` condition is smaller than 1.

````{admonition} Recommended SCHEME OF ADVECTION ... keywords
:class: tip
The {{ tm2d }} recommend specific combinations depending on the simulation scenario.

For models **without any dry zones** use:
```fortran
SCHEME FOR ADVECTION OF VELOCITIES : 4 / alternatively keep 1
SCHEME FOR ADVECTION OF TRACERS : 5
SCHEME FOR ADVECTION OF K-EPSILON : 4
```

For models with **tidal flats** use (like in this tutorial):
```fortran
SCHEME FOR ADVECTION OF VELOCITIES : 14 / alternatively keep 1
SCHEME FOR ADVECTION OF TRACERS : 5
SCHEME FOR ADVECTION OF K-EPSILON : 14
```
````

**Without any SCHEME FOR ADVECTION ...** keyword, the **SUPG OPTION** (Streamline Upwind Petrov Galerkin) keyword defines if upwinding applies and what type of upwinding applies. The **SUPG OPTION** is a list of four integers, where every element may take one of the following values:

* `0` disables upwinding,
* `1` enables upwinding with a classical SUPG scheme (recommended when the {term}`CFL` condition is unknown), and
* `2` enables upwinding with a modified SUPG scheme, where upwinding equals the {term}`CFL` condition (recommended when the {term}`CFL` condition is small).

The default is `SUPG OPTION : 2;2;2;2`, where

* the first list element refers to flow velocity (default `2`),
* the second to water depth (default `2` - set to `0` when `MATRIX STORAGE : 3`),
* the third to tracers (default `2`), and
* the last (fourth) to the k-epsilon model (default `2`).

Note that the `SUPG OPTION` keyword **is not optional** for many keyword combinations and this tutorial uses `SUPG OPTION : 0;0;2;2`.

**Implicitation parameters** (**IMPLICITATION FOR DEPTH**, **IMPLICITATION FOR VELOCITIES**, and **IMPLICITATION FOR DIFFUSION OF VELOCITY**) apply to the semi-implicit time discretization used in Telemac2d. To enable cross-version compatibility, implicitation parameters should be defined in the `*.cas` file. For **DEPTH** and **VELOCITIES** use values between `0.55` and `0.60` (**default is `0.55` since v8p1**); for **IMPLICITATION FOR DIFFUSION OF VELOCITY** set the v8p4 default of `1.0`.

The default `TREATMENT OF THE LINEAR SYSTEM : 2` involves so-called **mass lumping**, which leads to a smoothening of results. Specific mass lumping keywords and values are required for the flux control option of the **TREATMENT OF NEGATIVE DEPTHS** keyword and the default value for the treatment of tidal flats. To this end, the mass lumping keywords should be defined as:

```fortran
MASS-LUMPING ON H : 1.
MASS-LUMPING ON VELOCITY : 1.
MASS-LUMPING ON TRACERS : 1.
```

In addition, `MASS-LUMPING FOR WEAK CHARACTERISTICS : 1.` may be defined, which will make Telemac2d using weak characteristics (see below). The default value of any `MASS-LUMPING ...` keyword is `0.` and the maximum value is `1.`, which makes mass matrices diagonal.

The **OPTION OF CHARACTERISTICS** keyword defines the method of characteristics that can take a **strong (default of `1`)** or a **weak (`2`)** form. A weak form decreases {term}`Diffusion`, is more conservative, and increases computation time. Telemac2d automatically switches from the default strong (`1`) to the weak (`2`) form when

* the `TYPE OF ADVECTION` is set to `1`,
* any `SCHEME FOR ADVECTION ...` is set to `1`, or
* any `SCHEME OPTION FOR ADVECTION OF ...` is set to `2`.

None of these options should be used with tracers because they are not mass-conservative.

(steady2d-fv)=
### Finite Volumes

The finite volume method is mentioned here for completeness and detailed descriptions are available in section 7.2.2 of the {{ tm2d }}, and the malpasset example (`telemac/v8p4/examples/malpasset/`). To activate the finite volume scheme use:

```fortran
EQUATIONS : 'SAINT-VENANT FV' / the apostrophes are strictly needed here
```


```{admonition} Use finite volumes only with v8p2 or later
Earlier versions of Telemac2d's finite volume solver are buggy, but since major improvements were implemented with v8p2, the newest versions run stable.
```

The finite volume method involves the definition of a scheme through the **FINITE VOLUME SCHEME** keyword that can take one of the following integer values:

* `0` enables the {cite:t}`roe1981ars` scheme,
* `1` is the **default** and enables the kinetic scheme {cite:p}`audusse2000`,
* `3` enables the {cite:t}`zokagoa2010` scheme that is incompatible with tidal flats,
* `4` enables the {cite:t}`tchamen1998` scheme for modeling wetting and drying of a complex bathymetry,
* `5` enables the Harten Lax Leer-Contact (HLLC) scheme {cite:p}`toro2009a`, and
* `6` enables the Weighted Average Flux (WAF) {cite:p}`ata2012` scheme for which parallelism is currently not implemented.

The finite volume/elements schemes are (semi-) explicit and potentially subjected to instability. For this reason, a desired {term}`CFL` condition and a variable timestep are recommended to be defined:

```fortran
DESIRED COURANT NUMBER : 0.9
VARIABLE TIME-STEP : YES / default is NO
DURATION : 15000
```

The **DURATION** keyword is required to terminate the simulation.

The variable timestep will cause irregular listing outputs, while the graphic output frequency is written as a function of the above-defined **TIME STEP**. Note that **this tutorial uses `VARIABLE TIME-STEP : NO`**.

The **FINITE VOLUME SCHEME TIME ORDER** keyword defines the second-order time scheme, which is by default set to *Euler explicit* (`1`). Setting the time scheme order to `2` makes Telemac2d using the Newmark scheme where an integration coefficient may be used to change the integration parameter. Note that `NEWMARK TIME INTEGRATION COEFFICIENT : 1` corresponds to *Euler explicit*. To implement these options in the steering file, use the following settings:

```fortran
FINITE VOLUME SCHEME TIME ORDER : 2 / default is 1 - Euler explicit
NEWMARK TIME INTEGRATION COEFFICIENT : 0.5 / default is 0.5
```

However, other tutorials, and the Telemac forum recommend using the following scheme settings for finite volumes:

```fortran
FINITE VOLUME SCHEME : 5 / HLLC
FINITE VOLUME SCHEME SPACE ORDER : 1
FINITE VOLUME SCHEME TIME ORDER : 1
```


Additional keyword recommendations for the finite volume scheme are the following:

```fortran
OPTION FOR THE DIFFUSION OF VELOCITIES : 2 / only option to get mass conservation but can cause problems with tidal flats
SCHEME FOR ADVECTION OF VELOCITIES : 3 / use 3, also for FV - MATRIX STORAGE must be 3
SCHEME OPTION FOR ADVECTION OF VELOCITIES : 4 / overrides SUPG OPTION and OPTION FOR CHARACTERISTICS
NUMBER OF CORRECTIONS OF DISTRIBUTIVE SCHEMES : 2 / increase for higher accuracy and longer computing time, requires SCHEME OF ADVECTION 3,4,5, or 15 and OPTION 2,3,4
TYPE OF SOURCES : 2 / 2=Dirac is the only possibility for mass conservation, the default=1 means linear function and is not mass conservative
CONTINUITY CORRECTION : YES / particularly important when not only discharge but also depth is imposed at boundaries
```

Depending on the type of analysis, the solver-related parameters of **SOLVER**, **SOLVER OPTIONS**, **MAXIMUM NUMBER OF ITERATION FOR SOLVER**, and **TIDAL FLATS** may also be modified. Specifically, all **TIDAL FLAT** keywords become **obsolete with the FV scheme**.

(tm2d-solver-pars)=
### Numerical Solver Parameters

**The following descriptions refer to section 7.3.1 in the {{ tm2d }}.**

The solver can be selected and specified with the **SOLVER**, **SOLVER FOR DIFFUSION OF TRACERS**, and **SOLVER FOR K-EPSILON MODEL** keywords where the following settings are recommended:

```fortran
SOLVER : 1 / default is 3
SOLVER FOR DIFFUSION OF TRACERS : 1
SOLVER FOR K-EPSILON MODEL : 1
```

Setting the **SOLVER** to `1` instead of the default value of `3` is recommended with `TREATMENT OF THE LINEAR SYSTEM : 2` (i.e., the default since v8p2) for consistent and backward-compatible steering files.

Every solver keyword can take an integer value between `1` and `8`, where `1`-`6` use conjugate gradient methods:

* `1` sets the conjugate gradient method for symmetric matrices,
* `2` sets the conjugate residual method,
* `3` sets the conjugate gradient on normal equation method,
* `4` sets the minimum error method,
* `5` sets the squared conjugate gradient method,
* `6` sets the stabilized biconjugate gradient (BICGSTAB) method,
* `7` sets the *Generalised Minimum RESidual* (**GMRES**) method, and
* `8` sets the Yale University direct solver (YSMP) that is not compatible with parallelism.

The **GMRES method may be enabled with the finite element scheme** with the following solver options for the {term}`Krylov space`:

```fortran
SOLVER OPTION : 2 / hydrodynamic propagation
SOLVER OPTION FOR TRACERS DIFFUSION : 2 / tracer diffusion
OPTION FOR THE SOLVER FOR K-EPSILON MODEL : 2 /  k-e or Spalart-Allmaras
```

The solver options vary between values of **`2` for a small mesh** and **`5` for a large mesh**. Integers of `3` or `4` can be used for medium-sized meshes. The {{ tm2d }} recommends running simulations multiple times for finding an optimum value, where higher values (close to `5`) increase the time required for an iteration but lead to faster convergence.

(tm2d-accuracy)=
### Numerical Accuracy

**The following descriptions refer to section 7.3.2 in the {{ tm2d }}.**

The accuracy keywords make Telemac2d stop an iteration when two consecutive solutions for the same element vary by less than an **ACCURACY** threshold. To this end, the following default accuracy thresholds may be varied (Telemac2d ignores non-relevant parameters):

```fortran
SOLVER ACCURACY : 1.E-4 / propagation steps
ACCURACY FOR DIFFUSION OF TRACERS : 1.E-6 / tracer diffusion
ACCURACY OF K : 1.E-9 / diffusion and source terms of turbulent energy transport
ACCURACY OF EPSILON : 1.E-9 / diffusion and source terms of turbulent dissipation transport
ACCURACY OF SPALART-ALLMARAS : 1.E-9 / diffusion and source terms of the Spalart-Allmaras equation
```

In experience, the solver accuracy should not be larger than `1.E-3` (10$^{-3}$). In contrast, very small accuracies will lead to longer computation times. In addition or alternatively to the accuracy keywords, the following default numbers of maximum iterations can be modified to speed up calculations:

```fortran
MAXIMUM NUMBER OF ITERATIONS FOR SOLVER : 100 / maximum number of iterations when solving the propagation step
MAXIMUM NUMBER OF ITERATIONS FOR DIFFUSION OF TRACERS : 60 / tracer diffusion
MAXIMUM NUMBER OF ITERATIONS FOR K AND EPSILON : 50 / diffusion and source terms of k-e or Spalart-Allmaras
```

Telemac2d will print out warning messages when convergence could not be reached with the defined combination of accuracy and maximum iteration number keywords. The warning message printouts can be deactivated with the **INFORMATION ABOUT SOLVER** keyword, though deactivating convergence warnings is not recommended.

(tm2d-tidal)=
### Tidal Flats

**The following descriptions refer to section 7.5 in the {{ tm2d }}.**

The **TIDAL FLATS (default: `YES`)** keyword applies to the **finite elements scheme only ({ref}`EQUATIONS keyword <tm2d-numerical>`) and can be ignored with {ref}`finite volumes <steady2d-fv>`**. The term *tidal* may be slightly confusing because tidal flats can occur beyond coastal regions: Tidal flats can occur wherever wetting and drying of grid cells may occur or at flow transitions (e.g., when fast-flowing water enters a backwater zone). Wetting and drying, and flow transitions occur in almost all environments more complex than a square-like flume, and therefore, the activation of tidal flats in Telemac2d models is highly recommended. Though activating tidal flats leads to longer computation times, in most cases a calculation with tidal flats provides physically reasonable results.


The **TIDAL FLATS** keyword is linked with a couple of other Telemac2d keywords driving model stability and physical meaningfulness. The following keyword setups may be generally applied to (quasi) steady, real-world rivers and channels (as opposed to lab flumes with simplified geometries):

```fortran
TIDAL FLATS : YES
CONTINUITY CORRECTION : YES / default is NO
OPTION FOR THE TREATMENT OF TIDAL FLATS : 1
TREATMENT OF NEGATIVE DEPTHS : 2 / value 2 or 3 is required with tidal flats
```

The **OPTION FOR THE TREATMENT OF TIDAL FLATS** accepts integer values between `1` and `3` to select one of the following options:

* `1` detects tidal flats and corrects the free surface gradient.
* `2` removes tidal flat elements by using a masking table that eliminates any contribution of concerned mesh elements. This option may affect the mass conservation of the model.
* `3` resembles `1`, but adds a porosity term to half-dry mesh elements. This affects the amount of water in the model, which equals here the depth integral multiplied by the porosity. A user Fortran file may be used to modify the porosity term in the `USER_CORPOR` subroutine.

The **TREATMENT OF NEGATIVE DEPTHS (default: `1`)** keyword defines an approach for eliminating negative water depth values where the following integer numbers can be used:

* `0` disables any treatment of negative water depths.
* `1` conservatively smoothens negative water depths (**default**).
  * A float number keyword `THRESHOLD FOR NEGATIVE DEPTHS` (default: `0.`) is available only for this option.
  * Setting the threshold to, for instance, `-0.1` makes that negative water depths larger (e.g., -0.05 m) than -0.1 meters remain unchanged.
* `2` imposes a flux limitation that strictly ensures positive water depths.
* `3` acts similarly as `2` but for the ERIA {term}`Advection` scheme (set `SCHEME FOR ADVECTION OF TRACERS` to `4` or `5`). This option is appropriate for modeling conservative tracers.

````{admonition} TIDAL FLATS options require particular keyword combinations
:class: tip
The **SCHEME FOR ADVECTION ...** keywords (see the {ref}`finite element parameters <tm2d-fe>` section) must be set for **TRACERS** to LIPS (either `4` or `5`), and for all others to either a NERD (`13` or `14`) or the ERIA (`15`) scheme.

When using LIPS (`4` or `5`) with NERD (`13` or `14`) use the following combination (**used in this tutorial**):
```fortran
TIDAL FLATS : YES
OPTION FOR THE TREATMENT OF TIDAL FLATS : 1
TREATMENT OF NEGATIVE DEPTHS : 2
```

When using LIPS (`4` or `5`) with ERIA (`15`) use the following combination:
```fortran
TIDAL FLATS : YES
OPTION FOR THE TREATMENT OF TIDAL FLATS : 1
TREATMENT OF NEGATIVE DEPTHS : 3
```

Read more about viable or trouble-making parameter combinations for tidal flats in section 16.5 in the {{ tm2d }}.
````

### Matrix Handling

**The following descriptions refer to section 7.6 in the {{ tm2d }}.**

Telemac2d provides multiple options for matrix handling that need to be set up for particular solver schemes.

The **MATRIX STORAGE** keyword may be set to:

* `1` for using classic element-by-element matrix storage.
* `3` for using edge-based matrix storage (default). This default is required when any **SCHEME FOR ADVECTION ...** keyword is set to `3`, `4`, `5`, `13`, `14`, or `15`, and when any direct **SOLVER** is set to `8`.

The additional **MATRIX-VECTOR PRODUCT** keyword may be used to switch between multiplication methods for the finite element scheme. However, the default value of `1` (vector multiplication by a non-assembled matrix) should currently **not be changed** because the only alternative (`2` for frontal assembled matrix multiplication) is not implemented for parallelism and quasi-bubble discretization.


(tm2d-bounds)=
### Boundary Conditions

**The following descriptions of friction parameters refer to section 4.2 in the {{ tm2d }}.**

Liquid boundary keywords assign hydraulic properties to the spatially defined upstream and downstream liquid boundary lines in the Conlim (`*.cli`) file {ref}`created with BlueKenue <bk-liquid-bc>`. This section features the assignment of steady liquid boundaries for one discharge of 35 m$^3$/s. To this end, the upstream boundary condition is set to a steady target inflow rate (*Open boundary with prescribed Q*) and the downstream boundary condition gets a {term}`Stage-discharge relation` (*Open boundary with prescribed Q and H*) assigned (recall {numref}`Fig. %s <bk-bc-types>`). Thus, for running this tutorial add the following keywords to the steering (`*.cas`) file:

* The keyword `PRESCRIBED FLOWRATES : 35.;0.` assigns a flowrate of 35 m$^3$/s to the **upstream** boundary edge, and does not impose a flowrate on the **downstream** boundary edge. The downstream `Q` prescription of 0.0 makes Telemac2d ignore this value corresponding for the downstream boundary (prescribed depth only).
* The keyword `PRESCRIBED ELEVATIONS : 374.80565;371.33` assigns a water surface elevation $wse$ (or H in Telemac) in meters above sea level (m a.s.l.) to both the **upstream** and **downstream** boundaries.

The order of prescribed flowrates (Q) and $wse$ (H) values depends on the order of the definition of the boundaries. Thus, the first list element defines values for the upstream and the second list element for the downstream open boundary.

````{admonition} How to find out the order of boundary conditions?
:class: tip
The order of open boundaries can be read from the `*.cli` file. The first open boundary that is listed in the `*.cli` file corresponds to the first list element in any **PRESCRIBED ...** keyword. An open boundary node in the `*.cli` file is characterized by a line beginning with something like `4 5 5` or `5 5 5` (i.e., anything but `2 2 2`, which corresponds to a closed wall boundary node) and BlueKenue also marks the names of open boundaries at the line ends (after the hashtag). {numref}`Figure %s <boundary-cli>` illustrates the [boundaries.cli](https://github.com/hydro-informatics/telemac/raw/main/bk-slf/boundaries.cli) file used in this chapter where the `upstream` open boundary is defined at line 7, before the definition of the downstream open boundary starting at line 313.

```{figure} ../../img/telemac/boundary-cli.png
:alt: telemac 2d cli boundary conditions order cas steering file prescribed prescription
:name: boundary-cli

The [boundaries.cli](https://github.com/hydro-informatics/telemac/raw/main/bk-slf/boundaries.cli) file used in this tutorial starts with the upstream boundary defined in line 7. To find the downstream boundary scroll down to line 313.
```
````

Liquid boundary conditions may be assigned to any open boundary in the `*.cli` file.

````{admonition} External files instead of PRESCRIBED-keywords
:class: note, dropdown
Instead of a list of semi-colon separated numbers in the steering file, liquid boundary conditions can also be defined with a liquid boundary condition file in *ASCII* text format. For this purpose, the `LIQUID BOUNDARIES FILE` and/or `STAGE-DISCHARGE CURVES FILE` keywords need to be defined in the steering file. External files are required for the simulation of quasi-unsteady flows (e.g., a flood hydrograph or low flow sequences for habitat conditions) and more details can be found in sections 4.2.5 and 4.2.6 in the {{ tm2d }} or the {ref}`unsteady section in this eBook <chpt-unsteady>`.
````

A velocity profile type can be assigned to any prescribed Q (flowrate) or prescribed U (velocity) open boundary in the form of a list that has the same element order as the above-defined **PRESCRIBED ...** keywords. For this purpose, upstream and downstream velocity profiles can be defined with the **VELOCITY PROFILES** keyword that accepts the following values:

* `1` is the **default** option that defines the flow velocity direction at the boundary nodes normal to their edges. This option assigns a length of 1 to the vector and multiplies it with a numeric factor to yield a target flowrate.
* `2` reads U and V velocity profiles from the boundary conditions (`*.cli`) file, which are multiplied with a constant to yield a target flowrate.
* `3` imposes the velocity vector direction normal to the boundary and reads the value (UBOR) from the `*.cli` file, which is then multiplied by a constant to yield a target flowrate.
* `4` imposes the velocity vector direction normal to the boundary and calculates the value's norm proportional to the square root of the water depth. This option can only be used with a *prescribed Q* open boundary.
* `5` imposes the velocity vector direction normal to the boundary and calculates the value's norm proportional to the square root of a virtual water depth.

With the upstream boundary being a *prescribed Q* boundary, this tutorial uses `VELOCITY PROFILES : 4;1` in the steering file. Read more about options for defining velocity profiles in section 4.2.8 of the {{ tm2d }}.


```{admonition} Boundary conditions and mass balance
:class: tip

The boundary condition settings affect mass balance, which is a crucial criterion for a sound numerical model. Read more in the spotlight focus on setting up {ref}`boundary conditions for mass balance <foc-mass-bc>`.
```


(tm2d-init-dry)=
### Initial Conditions

**The following descriptions refer to section 4.1 in the {{ tm2d }}.**

The initial conditions describe the state of the model at the beginning of a simulation. Telemac2d recognizes the following types of initial conditions, which can be defined in the steering file with the keyword `INITIAL CONDITIONS : 'TYPE'` where `TYPE` can be one of the following:

* `ZERO ELEVATION` initializes the free surface elevation at 0 (**default**). Thus, the initial water depths correspond to the bottom elevation.
* `CONSTANT ELEVATION` initializes the free surface elevation at a value defined with an **INITIAL ELEVATION** keyword that has a default value of `0.`. Thus, the initial water depths correspond to the subtraction of the bottom elevation from the water surface elevation $wse$. The initial water depth is set to zero at nodes where the bottom elevation is higher than defined by the **INITIAL ELEVATION** keyword.
* `ZERO DEPTH` initializes the simulation with `0` (i.e., $wse$ corresponds to bottom elevation). Thus, the model starts with dry conditions, similar as in the {ref}`BASEMENT <basement2d>` tutorial.
* `CONSTANT DEPTH` initializes the water depths at a value defined by an **INITIAL DEPTH** keyword that has a default value of `0.`.
* `TPXO SATELLITE ALTIMETRY` initializes the model using information provided by a user-defined database (e.g., the [OSU TPXO model for ocean tides](http://g.hyyb.org/archive/Tide/TPXO/TPXO_WEB/global.html)). Read more in section 4.2.12 of the {{ tm2d }} on modeling marine systems.

To begin, define the initial water depth as 0 with the following keyword, which means that the model will be initialized with a dry riverbed:

```fortran
INITIAL CONDITIONS : 'ZERO DEPTH'
```

The simulation speed can be significantly increased when the model has already been running once at the same (initial) discharge. The result of an earlier simulation can be used for the initial condition with the `COMPUTATION CONTINUED : YES` (default is `NO`) and `PREVIOUS COMPUTATION FILE : *.slf` (provide the name of a `*.slf` file) keywords. This type of model initialization is also referred to as *hotstart*. Read more about hotstarts in the {ref}`unsteady simulation <tm2d-hotstart>` and {ref}`Gaia <gaia-hotstart>` sections. Also section 4.1.3 in the {{ tm2d }} provides descriptions for continuing (hotstart) calculations.


````{admonition} Wet the model at the beginning

Use a *constant* water *depth* initial condition of `1` (integer) to speed up calculations, which corresponds to a completely flooded initial model state (i.e., water volume surplus). However, this type initialization will also place a 1-m thick water layer beyond the riverbanks, where water may not be able to run off. Thus, puddles are likely to form longitudinally along the solid `2 2 2` boundaries.

```fortran
INITIAL CONDITIONS : 'CONSTANT DEPTH'
INITIAL DEPTH : 1
```

In the case of delta simulations, an initial condition defined with `CONSTANT ELEVATION` might be preferably defined at a lake or sea level.
````

(tm2d-friction)=
### Friction (Roughness)

**The following descriptions of friction parameters refer to section 6.1 in the {{ tm2d }}.**

The **LAW OF BOTTOM FRICTION** keyword defines a friction law for topographic boundaries, which can be set to:

* `0` for no friction.
* `1` for the {cite:t}`haaland1983` equation, which is an implicit form of the {cite:t}`colebrook1937` equation that builds on the Darcy-Weisbach friction factor $f_D$. This law involves a high degree of uncertainty that stems from the experimental dataset of the original author.
* `2` for the {cite:t}`chezy_formula_1776` roughness that can be similarly used as `3` and `4`.
* `3` for {cite:t}`strickler_beitrage_1923` roughness $k_{st}$ (read more, for example, in the {ref}` 1d hydraulics exercise <ex-1d-hydraulics>`), which is the inverse of $n_m$ (`4`).
* `4` for {cite:t}`manning_transactions_1891` roughness $n_m$ (read more, for example, in the {ref}` 1d hydraulics exercise <ex-1d-hydraulics>`), which is the inverse of $k_{st}$ (`3`).
* `5` for the {cite:t}`nikuradse_stromungsgesetze_1933` roughness law, which should correspond to 3 $\cdot D_{90}$ according to {cite:t}`vanrijn2019`.
* `6` for the logarithmic law of the wall for turbulent flows. This option assumes that the average flow velocity is a logarithmic function of the distance from the wall beyond the viscous and buffer layers. The thickness of these layers is a function of the wall roughness length {cite:p}`von_karman_mechanische_1930`.
* `7` for the {cite:t}`colebrook1937` equation that calculates the Darcy-Weisbach friction factor $f_D$ for turbulent flows in smooth pipes.

With respect to the 2d applications in this eBook, the most relevant bottom friction laws are `3` {cite:p}`strickler_beitrage_1923`, `4` {cite:p}`manning_transactions_1891`, and `6` (log law). The {cite:t}`nikuradse_stromungsgesetze_1933` roughness law (`5`) is recommended for 3d simulations (see the {ref}`Telemac3d tutorial <tm3d-hydrodynamics>`). Friction is more generally referred to as with the general coefficient $c_{f}$, which has a particular relevance for {term}`bedload <Bedload>` transport (cf. {ref}`morphodynamic calculations with Gaia <c-friction>`).

The **FRICTION COEFFICIENT FOR THE BOTTOM** keyword sets the value for a characteristic roughness coefficient. For instance, when the friction law keyword is set to `3` {cite:p}`strickler_beitrage_1923`, the friction corresponds to the Strickler roughness coefficient $k_{st}$ (in fictive units of m$^{1/3}$ s$^{-1}$). For rough channels (e.g., mountain rivers) $k_{st} \approx 20$ m$^{1/3}$ s$^{-1}$ and for smooth concrete-lined channels $k_{st} \approx 75$ m$^{1/3}$ s$^{-1}$. In fully turbulent flows, the Strickler roughness can be approximated with $k_{st} \approx \frac{26}{D_{90}^{1/6}}$ {cite:p}`meyer-peter_formulas_1948` where $D_{90}$ is the grain diameter of which 90% of the surface grain mixture are finer.
This tutorial features the application of a *Manning* roughness coefficient of $n_m$= 0.03, which is the inverse of $k_{st}$ and implemented with:

```fortran
LAW OF BOTTOM FRICTION : 4 / 4-Manning
FRICTION COEFFICIENT : 0.03 / Roughness coefficient
```

````{dropdown} Expand to see exemplary values for Manning roughness
{numref}`Table %s <tab-mannings-n>` lists exemplary values for the Manning roughness coefficient $n_m$ based on {cite:t}`usgs1973_n` and {cite:t}`usgs1989_n`.

```{list-table} Exemplary values for Manning roughness for straight uniform channels.
:header-rows: 1
:name: tab-mannings-n

* - Surface type
  - Material diameter (10$^{-3}$m)
  - $n_m$ (m$^{-1/3}$s)

* - Concrete
  - $-$
  - 0.012-0.018

* - Firm soil
  - $-$
  - 0.025-0.032

* - Coarse sand
  - 1-2
  - 0.026-0.035

* - Gravel
  - 2-64
  - 0.028-0.035

* - Cobble
  - 64-256
  - 0.030-0.050

* - Boulder
  - $>$ 256
  - 0.040-0.070
```
````

```{admonition} Friction zones (regional friction values)
:class: tip, dropdown

To create zones with different friction values, have a look at the spotlight focus on {ref}`roughness zones <tm-friction-zones>`.
```

In addition, specific roughness conditions should be defined for the liquid boundaries (see {ref}`above <tm2d-bounds>`), which should not be changed in the process of model calibration later. To this end, a **measured {term}`stage-discharge relation <Stage-discharge relation>`** is required to back-calculate cross-section averaged hydraulics. For this purpose, take a look at the {ref}`Python exercise on 1-d hydraulics for solving the Manning-Strickler <ex-1d-hydraulics>` formula.

```fortran
LAW OF FRICTION ON LATERAL BOUNDARIES : 3 / integer (3 is Strickler)
ROUGHNESS COEFFICIENT OF BOUNDARIES : 33.3 / float inverse of n_m=0.03
```

```{admonition} Differentiate between bottom and boundary friction
:class: important

Not using the two keywords defining the friction at the boundaries will make that any roughness calibration affects the mass balance.
```


(tm2d-turbulence)=
### Turbulence

**The following descriptions refer to section 6.2 in the {{ tm2d }}.**

Turbulence describes a seemingly random and chaotic state of fluid motion in the form of three-dimensional vortices (eddies). True turbulence is only present in 3d vorticity and when it occurs, it mostly dominates all other flow phenomena through increases in energy dissipation, drag, heat transfer, and mixing {cite:p}`kundu_fluid_2008`. The phenomenon of turbulence has been a mystery to science for a long time, since turbulent flows ({term}`read more about the implementation in RANS <RANS>`) have been observed, but could not be explained by the linear equations systems. Today, turbulence is considered a random phenomenon that can be accounted for in linear equations, for instance, by introducing statistical parameters. For instance, when turbulence applies to the depth-averaged {term}`Navier-Stokes equations` a numerical solution for a quantity (e.g., flow velocity) corresponds to $value = \overline{mean value} + value fluctuation'$. For this purpose, there are a variety of options for implementing turbulence in numerical models {cite:p}`nezu1993`.

The horizontal and vertical dimensions of turbulent eddies can vary greatly, especially in rivers and transitions to backwater zones (tidal flats) where the wide horizontal flow dimension (river width $w$) is significantly larger than the vertical flow dimension (water depth $h$): $w >> h$. Telemac2d provides multiple turbulence models that can be applied to the vertical and/or horizontal dimensions and defined with the **TURBULENCE MODEL** (see also: {term}`RANS`) keyword being an integer number for one of the following options:

* `1` to use a constant viscosity coefficient (**default**) for turbulent viscosity, molecular viscosity, and {term}`Diffusion`. This closure option should not be used with {term}`Stage-discharge relation` open boundaries (i.e., do not use with prescribed Q and H) {cite:p}`wilson2002`.
* `2` to use the Elder formula for the {term}`Diffusion` coefficient $D$. The Elder turbulence closure also yields small errors for {term}`Stage-discharge relation` open boundaries (i.e., do not use this option with prescribed Q and H) {cite:p}`wilson2002`.
* `3` to use the $k-\epsilon$ two-equation model solving the {term}`Navier-Stokes equations`. The first equation represents a turbulence closure for the {term}`turbulent kinetic energy <Turbulent kinetic energy>` $k$; the second equation is a turbulence closure for the turbulent dissipation $\epsilon$. Both equations express that the sum of change of (I) $k$ and $\epsilon$ in time, and (II) {term}`Advection` transport of $k$ and $\epsilon$ equal the sum of (1) {term}`Diffusion` transport of $k$ and $\epsilon$, (2) the production rate of $k$/$\epsilon$, and (3) the destruction rate of $k$/$\epsilon$ {cite:p}`launder1974`. The $k-\epsilon$ model is a generalization of the mixing length model (see option `5`) and assumes that the turbulent viscosity is isotropic (valid for many river applications, but not for circular-rotating flows or groundwater) {cite:p}`bradshaw1987`. Thus, the $k-\epsilon$ model introduces two additional equations and requires a finer mesh than the constant viscosity option `1`, which leads to a longer computation time. Yet, the $k-\epsilon$ model generally yields accurate results and small errors with {term}`Stage-discharge relation` open boundaries {cite:p}`wilson2002`. The following default keywords are associated with the $k-\epsilon$ model:
  * `VELOCITY DIFFUSIVITY : 1.E-6` corresponding to the kinematic viscosity $\nu$ of water (10$^{-6}$ m$^2$/s).
  * `TURBULENCE REGIME FOR SOLID BOUNDARIES : 2` **for rough walls** of closed boundaries to apply the value chosen for the **LAW OF BOTTOM FRICTION** and **ROUGHNESS COEFFICIENT OF BOUNDARIES** keywords (recall section {ref}`tm2d-friction`). For **smooth closed boundary walls** set `TURBULENCE REGIME FOR SOLID BOUNDARIES : 1`.
  * `INFORMATION ABOUT K-EPSILON MODEL : YES` enables console output of information on the $k-\epsilon$ closure solution.
* `4` to use the {cite:t}`smagorinsky1963` (also known as *general circulation*) model, which stems from climate modeling and is appropriate for modeling maritime systems with large eddies. The {cite:t}`smagorinsky1963` model does not account for {term}`Diffusion`.
* `5` to use a mixing length model according to Prandtl's theory that a fluid quantity conserves its properties for a characteristic length before it mixes with the bulk flow {cite:p}`bradshaw1974`.
* `6` to use the {cite:t}`spalart1992` model that solves the {term}`Continuity equation` for a viscosity-like, kinematic eddy turbulent viscosity. The {cite:t}`spalart1992` model was originally developed for aerodynamic flows with low {term}`Reynolds number` and it has also shown good results for other applications.

This tutorial uses the $k-\epsilon$ model (`3`) because of its low error rate and wide applicability (compared to other turbulence closures).

```
DIFFUSION OF VELOCITY : YES / enabled by default
TURBULENCE MODEL : 3
```


(tm2d-run)=
## Run Telemac2d

With the steering (`*.cas`) file, the last necessary ingredient for running a steady hydrodynamic 2d simulation with Telemac2d is available. Make sure to put all required files in one simulation folder (e.g., `~/telemac/v8p4/mysimulations/steady2d-tutorial/`). The required files can also be downloaded from this eBook's [steady2d tutorial repository](https://github.com/hydro-informatics/telemac/tree/main/steady2d-tutorial/) and include:

* [qgismesh.slf](https://github.com/hydro-informatics/telemac/raw/main/steady2d-tutorial/qgismesh.slf)
* [boundaries.cli](https://github.com/hydro-informatics/telemac/raw/main/steady2d-tutorial/boundaries.cli)
* [steady2d.cas](https://github.com/hydro-informatics/telemac/raw/main/steady2d-tutorial/steady2d.cas)

With these files prepared, load the TELEMAC environment, and run Telemac2d following the explanations in the next sections.

### Load environment and files

Go to the configuration folder of the TELEMAC installation (e.g., `~/telemac/v8p4/configs/`) and load the environment (e.g., `pysource.gfortranHPC.sh` - use the same as for compiling TELEMAC).

```
cd ~/telemac/v8p4/configs
source pysource.gfortranHPC.sh
```

````{admonition} If you are using the Hydro-Informatics (Hyfo) Mint VM
:class: note, dropdown

If you are working with the {ref}`Mint Hyfo VM <hyfo-vm>`, load the TELEMAC environment as follows:

```
cd ~/telemac/v8p2/configs
source pysource.hyfo-dyn.sh
```
````

### Start a Telemac2d simulation

To start a simulation, change to the directory (`cd`) where the simulation files live and run the steering file (`.cas`) with the **telemac2d.py** script:

```
cd ~/telemac/v8p4/mysimulations/steady2d-tutorial/
telemac2d.py steady2d.cas -s
```

The `-s` flag is not strictly needed but useful for revising simulation characteristics, such as fluxes across the liquid boundaries or the total simulation time. It will write a file named `steady2d.cas.[...].sortie` and can be used for convergence analysis described in the spotlight chapter on {ref}`quantitative convergence <tm-convergence>`.

As a result, a successful computation should end with the following lines (or similar) in *Terminal*:

```fortran
[...]
                     *************************************
                     *    END OF MEMORY ORGANIZATION:    *
                     *************************************

 CORRECT END OF RUN

 ELAPSE TIME :
                             03  MINUTES
                             44  SECONDS
... merging separated result files

... handling result files
        moving: r2dsteady.slf
... deleting working dir

My work is done
```

Thus, Telemac2d produced the file *r2dsteady.slf* that can now be analyzed in the {ref}`post-processing with QGIS <tm-steady2d-postpro>` or ParaView.


(tm-steady2d-postpro)=
# Post-processing

The post-processing of the steady 2d scenario uses QGIS and the {ref}`PostTelemac plugin <tm-qgis-plugins>`. Alternatively, Telemac results can also be visualized with [ParaView](https://www.paraview.org) or BlueKenue.

## Load Results

Launch QGIS, {ref}`create a new QGIS project <qgis-project>`, set the project {term}`CRS` to `UTM zone 33N`, add a satellite imagery {ref}`basemap <basemap>`, and save the project (e.g., as `tm2d-postpro.qgis`) in the same folder where the Telemac2d simulation results file (*r2dsteady.slf* is located), similar to the descriptions in the {ref}`pre-processing tutorial <tm-qgis-prepro>`.

Load the `r2dsteady.slf` geometry file as mesh layer with drag and drop from the Browser panel to the Layers panel. Make sure to import it with its correct georeference: **EPSG:32633** (ETRS 89 / UTM zone 33N).


````{admonition} The PostTelemac Plugin
:class: tip, dropdown

The PostTelemac plugin provides useful routines for mesh analysis, in particular, for older QGIS versions. However, QGIS now has powerful mesh analysis tools that enable insights into Telemac results. To work with the PostTelemac plugin, open it as indicated in {numref}`Fig. %s <open-post-tm>`.

```{figure} ../../img/telemac/load-tm-plugin.png
:alt: qgis load open PostTelemac plugin
:name: open-post-tm

Open the PostTelemac plugin in QGIS.
```

The PostTelemac plugin typically opens as a frame at the bottom-right of the QGIS window (maybe hard to find the first time). Detach the PostTelemac plugin from the main QGIS window by clicking on the resize window button in the top-right corner of the PostTelemac plugin frame (next to the *close* cross). In the detached window load the model results as follows (also indicated in {numref}`Fig. %s <post-tm>`):

* Click on the **File ...** button, navigate to the location where the simulation lives and select `r2dsteady.slf`.
* **Move the Time slider** to the last timestep (e.g., `15000`) and observe the main window, which will show by default the VELOCITY U parameter in this tutorial (depends on the variables defined with the `VARIABLES FOR GRAPHIC PRINTOUTS` keyword).
* Familiarize with the PostTelemac plugin by modifying the display **Parameter** and the **Color gradient**.

```{figure} ../../img/telemac/post-telemac.png
:alt: qgis load simulation results slf PostTelemac plugin
:name: post-tm

Load the Telemac2d simulation results file in the detached PostTelemac plugin window.
```

Once imported, the *r2dsteady* layer is listed in the *Layers* panel of QGIS (typically in the bottom-left of the window). Double-clicking on the *r2dsteady* layer will re-open the PostTelemac plugin when it was closed (e.g., after restarting QGIS). Structurally, the *r2dsteady* layer is a mesh with a particular format.

***

To export a flow velocity raster at the simulation end time (in this example `8000`). For this purpose, click on the **RasterCreation** entry of the **Export** menu in the **Tools** tab. Then:

* Set the **time step** to the maximum (use the field indicated in {numref}`Fig. %s <posttm-export-tif>`).
* Select `6 : VITESSE` for **Parameter**.
  * *Vitesse* is French for *velocity* and it is calculated as $VITESSE = \sqrt{(VELOCITY\ U)^2 + (VELOCITY\ V)^2}$
  * Note that `VELOCITY U` and `VELOCITY V` are the flow velocities in $x$ and $y$ directions, respectively.
* In the **Group** frame set:
  * **Cell size** to `1`, and
  * **Extent** to `Full Extent`.
* Start the export by clicking on **Create raster**.

The processing frame can be found at the bottom of the window (scroll down by clicking on the dotted circle indicated in {numref}`Fig. %s <posttm-export-tif>`) and informs about the progress.

```{figure} ../../img/telemac/posttm-export-tif.png
:alt: qgis export simulation results slf PostTelemac raster geotiff tif
:name: posttm-export-tif

Export a flow velocity raster of simulation results with the PostTelemac plugin (the screenshot uses a maximum time of 8000).
```

The successful raster creation results in a new layer called **r2dsteady_raster_VITESSE**, which is automatically saved as a {term}`GeoTIFF` raster in the same folder where the QGIS project (`*.qgz`) and the `r2dsteady.slf` files are located. {numref}`Figure %s <exported-tif>` in the {ref}`below-shown wet initialization exercise <tm2d-init-wet>` displays the exported flow velocity raster in QGIS with a *Magma* color map (select in the layer symbology).


```{admonition} Export to shapefile or mesh
:class: tip
The PostTelemac plugin also enables exporting to other geodata types such as vector shapefiles or meshes.
```

***

In addition, the evolution of a parameter over the simulation time can be exported to a video with the PostTelemac plugin. For this purpose, go to the **Tools** tab (light blue box in {numref}`Fig. %s <post-tm>`) and follow the descriptions in the following paragraphs.

For example, to export flow rates (fluxes) along any line or at any node of the mesh, make sure that `Q` is in the list of the `VARIABLES FOR GRAPHIC PRINTOUTS` keyword. Then, go to the **Tools** tab of the PostTelemac plugin in QGIS and:

* Click on the **Flow** ribbon.
* In the **Selection** frame select **Temporary polyline** and move the mouse cursor on the map viewport where the cursor should turn into a black cross that enables drawing a (green) thick line anywhere in the mesh layer (*r2dsteady*). If the cursor does not enable drawing, go somewhere else in the PostTelemac plugin (e.g., to the *Samplingtool* ribbon), then go back to the *Flow* ribbon, click in the *Selection* frame, and re-try. To draw a line for exporting associated flows:
  * left-click with the mouse cursor somewhere on the *r2dsteady* mesh on the map (e.g., the left bank at the inflow open boundary), and
  * double left-click on another point on the *r2dsteady* mesh (e.g., the right bank at the inflow open boundary indicated in {numref}`Fig. %s <draw-flow-controls-pt>`).
  * The PostTelemac plugin then automatically draws the shortest path between the two points along the mesh nodes.
* The flowrate across the green line is now plotted in the graph of the PostTelemac plugin for the simulation time (e.g., timesteps `0` to `8000`).
* To save the values for comparison at another line, click on **Copy to clipboard** and paste the values into a spreadsheet (office software,  such as {ref}`Libre Office <lo>`).

**Repeat** the procedure **at the downstream open boundary** and paste the values in another column of the spreadsheet used for the upstream open boundary.

```{figure} ../../img/telemac/flow-control-us-pt.png
:alt: qgis flow rate discharge control section Post Telemac convergence
:name: draw-flow-controls-pt

Draw polylines along mesh nodes and export associated flows (Copy to clipboard; background map: {cite:t}`googlesat` satellite imagery).
```

The extracted data with the PostTelemac plugin were used in the {ref}`wet initialization exercise below <tm2d-init-wet>`.

````

(tm2d-post-export)=
## Export to GeoTIFF

To export the model results to a {term}`GeoTIFF` raster, go to the **Processing Toolbox** (in QGIS), expand the **Mesh** entry, and open the **Rasterize mesh dataset** tool. In the **Rasterize Mesh Dataset** popup window ({numref}`Figure %s <rasterize-v-mesh>`) make the following settings:

* **Input mesh layer**: select the Telemac results mesh layer (`r2dsteady`)
* **Dataset groups**: click on the **...** button > **Select in Available Dataset Groups** and select a quantity of interest. This tutorial features the export of a flow velocity. Click **OK** to return to the Rasterize Mesh Dataset tool.
* **Dataset time**: click on the up/down arrow symbol to scroll to the bottom and select the last timestep. In an unsteady (i.e., quasi-steady) simulation, other timesteps might be of interest, too.
* **Extent**: click on the dropdown arrow > **Calculate from Layer** > select **r2dsteady**
* **Pixel size**: `1.0` (default). With coarser or finer meshes, the pixel size should be varied.
* **Output coordinate system**: select `EPSG:32633` (that is, the coordinate reference system of the mesh)
* **Output raster layer**: click on **...** to navigate to a target folder and enter a name for the raster. Here: `velocity-tmax.tif`.
* **Run** the rasterization.


```{figure} ../../img/telemac/rasterize-mesh.png
:alt: telemac qgis export velocity geotiff raster
:name: rasterize-v-mesh

The Rasterize Mesh Dataset tool in QGIS.
```

The resulting **velocity-tmax** raster will be added to the Layers panel. For better visualization, some color is helpful. Therefore, double-click on the new **velocity-tmax** to open its properties. Go to the **Symbology**, change the **Render type** to `Singleband pseudocolor`, and use your favorite color ramp and number of classes for visualizing the velocity. To make `0`-entries invisible, click on their **Color** symbol and set the **Opacity** to 0%, or set the **Min** to `0.0001`.


```{figure} ../../img/telemac/qgis-exported-v.png
:alt: qgis telemac flow velocity vitesse results slf raster geotiff tif
:name: qgis-exported-v

The exported flow velocity (VITESSE) GeoTIFF raster in QGIS (background map: {cite:t}`googlesat` satellite imagery). The location of the Raster mesh dataset tool in the Processing Toolbox is highlighted on the right.
```


## Analyze Results

The first analysis of results should address the basic correctness of the model, for instance, regarding mass balance and its evolution over time. For this purpose, open the **Time Controller** <img src="../../img/qgis/time-controller.png" width="15" height="15"> in QGIS top menu.


(verify-steady-tm2d)=
### Quantitative Discharge Convergence

During the simulation, the keywords `MASS-BALANCE : YES` and/or `PRINTING CUMULATED FLOWRATES : YES` print mass fluxes across liquid boundaries in the Terminal. To retrospectively review flux rates and volume balance, the simulation must have run with the `-s` flag, which saves the simulation state in a file called similar to `steady2d.cas_YEAR-MM-DD-HHhMMminSSs.sortie`. Based on the `.sortie` file, sums of fluxes, the total volume, and volume error can be extracted and analyzed with the Python scripts provided along with the Telemac installation (*HOMETEL/scripts/python3/*). The Telemac Jupyter notebooks (*HOMETEL/notebooks/* > *data_manip/extraction/\*.ipynb* or *workshops/exo_fluxes.ipynb*) exemplify the usage of the Python scripts. A detailed discussion on convergence and tweaked Python scripts ([pythomac](https://pythomac.readthedocs.io)) can be found in this eBook, in the chapter on {ref}`quantitative Telemac convergence analysis <tm-convergence>`. With these scripts, {numref}`Fig. %s <steady-flux-convergence-standalone>` was generated showing the flows across the two boundaries of the steady-2d study, indicating convergence after approximately 7000 timesteps.

```{figure} ../../img/telemac/steady-flux-convergence.png
:alt: python telemac flux discharge convergence pythomac
:name: steady-flux-convergence-standalone

Flux convergence plot across the two boundaries of the dry-initialized steady Telemac2d simulation (created with pythomac).
```

(qualitative-postel)=
### Qualitative Velocity, Depth, and Discharge Evolution

The convergence of water depth and flow velocity, and therefore, discharge, can be qualitatively observed in QGIS through the **Time Controller** (see activation in {numref}`Fig. %s <qgis-time-controller-tm>`). The frequency of images can be set through clicking on the cogwheel of the time controller, and image sequences played by clicking in the *Play* button. Additionally, {numref}`Fig. %s <qgis-time-controller-tm>` uses an overlay of water depth pixel colors (contour plot), and flow velocity vectors, defined in the *Layer Styling* panel. The North and discharge arrows, and the title are *Decorators*, which can be found in **View** > **Decorators**.

```{figure} ../../img/telemac/qgis-time-controller.jpg
:alt: time controller qgis telemac
:name: qgis-time-controller-tm

The activated time controller in QGIS enables to move along the time axis of modeled quantities (background map: {cite:t}`googlesat` satellite imagery). The red-highlighted buttons activate the time controller, play the sequence of images of selected quantities, provide a setting for playing a frequency of images per second, and enable saving images of all timesteps (see instructions below).
```

To **export a series of images for turning them into a movie-like GIF**, use the **Save** button of the time controller. Set up the desired resolution and define an output folder. The series of PNG images can then be converted, for example, with [GIMP](https://www.gimp.org/), into a GIF. For this purpose, download and open GIMP, then:

* Open the first image of the exported series.
* Pull all other exported images into the *Layers* panel of GIMP.
* Reverse the order of the layers in GIMP: **Layer** > **Stack** > **Reverse Layer Order**.
* Save the image as GIF: **File** > **Export As...**.
* Select a folder to save the file, in the **Name** field enter `[any-name].GIF`, and click **Export**.
* In the popup window enable **As animation** and **Loop forever** with a recommended delay between frames of **100 milliseconds**. Keep all other defaults and click **Export**.

The animated figure below features an exported GIF with water depth in the background and flow velocity as streamline-vectors ranging from 0 to 2.0 m/s. The animation shows how the model is filled from both its upstream (left) and downstream (right) boundaries at the beginning of the simulation. While the upstream discharge was imposed along with a water depth through a `5 5 5` boundary, the downstream boundary only had a prescribed water depth `5 4 4` boundary. The prescription of sufficient water depths was necessary to avoid supercritical flows at the boundaries, which would make the numerical model crash immediately. Because the flux coming from the downstream boundary needs to move uphill, it cannot go very fast and is rolled over by a wave of water coming from the upstream boundary. If a downstream flux was prescribed, the model would have been more unstable and overdetermined. 

````{div} full-width
```{admonition} GIF sequence of a dry-initialized Telemac2d model (large file size!)
:class: tip, dropdown
:name: telemac-flow-convergence-gif

<img src="https://github.com/hydro-informatics/media/blob/main/gif/inn-dry-init.gif?raw=true" alt="Telemac dry-init GIF" />

```
````

```{admonition} Recall: boundary conditions and mass balance
:class: important

Mass balance is a crucial criterion for a sound numerical model. Read more in the spotlight chapter on setting up {ref}`boundary conditions for mass balance <foc-mass-bc>`.
```


(tm2d-init-wet)=
# Modify Initial Conditions

The above {numref}`Fig. %s <steady-flux-convergence>` and {ref}`depth-velocity animation <telemac-flow-convergence-gif>` point to stability achieved after approximately 7000 timesteps. A wet-initialized model converges much faster, but either requires a previous run of a dry model initialization, or it can make use of other initial condition keywords in Telemac. Ideally, the dry-initialized model is used as a so-called hotstart condition for a wet-initialized model, as described in the {ref}`unsteady 2d tutorial <tm2d-hotstart>`. 
  
````{admonition} Challenge: initialize Telemac with an initial water depth
:class: important

Even though it is not best practice for modeling a river, running the steady2d.cas simulation with an initial water depth of 1 m is an interesting exercise to experience why it is not a good choice. To run the model with an initial water depth, we can facilitate the boundary conditions by removing the upstream depth constraint (i.e., setting the first entry of the `PRESCRIBED ELEVATIONS` keyword to `0.`), and modifying the `INITIAL [...]` condition keywords to a `'CONSTANT DEPTH'` of `1` meter:

```fortran
/ steady2d_wet.cas
/ ... header
/ ...
PRESCRIBED FLOWRATES  : 35.;0.
PRESCRIBED ELEVATIONS : 0.;371.33
/ ...
INITIAL CONDITIONS : 'CONSTANT DEPTH'
INITIAL DEPTH : 1
/ ...
/ ... footer
```

Also, change the upstream boundary type to a less constrained `4 5 5` (prescribed Q only) type in the  {ref}`boundaries.cli <bk-liquid-bc>` file. For this modification, it is sufficient to **open boundaries.cli in any text editor** and use its **find-and-replace** function (e.g., `CTRL` + `H` keys in {ref}`npp`):

* In the **Find** field type `5 5 5`.
* In the **Replace with** field type `4 5 5`.
* Click on **Replace** until all **upstream** boundary node types are modified.
* Save and close **boundaries.cli**.

Save the modified `.cas` and `.cli` files, and re-run Telemac:

```
telemac2d.py steady2d_wet.cas
```

The diagram in {numref}`Fig. %s <convergence-diagram-tm2d-wet>` plots the two columns of flows at the upstream and downstream open boundaries over time for the simulation setup in this tutorial. The diagram suggests that the model reaches stability (i.e., converges) after the 55th output listing (simulation time $t \leq 5500$).

```{figure} ../../img/telemac/convergence-diagram-tm-pt.png
:alt: telemac2d convergence steady simulation wet initialization
:name: convergence-diagram-tm2d-wet

Convergence of inflow (upstream) and outflow (downstream) at the open model boundaries of a wet model.
```

```{figure} ../../img/telemac/qgis-exported-tif.png
:alt: qgis flow velocity vitesse results slf PostTelemac raster geotiff tif
:name: exported-tif

The flow velocity (VITESSE) GeoTIFF raster after a wet-initialized Telemac simulation, shown in QGIS (background map: {cite:t}`googlesat` satellite imagery).
```

```{admonition} Question: what are the pros and cons of the wet-initialized simulation with constant water depth?
:class: note, dropdown

**Positive (pro)** is that the simulation converges considerably faster than in the case of the dry-initialized model.

**Negative (contra)** performance indicators are some non-zero flow velocity pixels on the floodplains (beyond the riverbanks) in {numref}`Fig. %s <exported-tif>`. These apparently wrongly modeled pixels are an artifact of the use of wet initial conditions, which put a 1-m thick water layer all over the model. Specifically, water patches remained in small, local terrain depressions between the solid boundaries (`2 2 2`) and dykes along the riverbanks. This water could not run off and stayed on these patches until the end of the simulation. This is a physically unreasonable no-go flag, which disqualifies a model for any application.
```
````

(tm2d-calibration)=
# Notes on 2d Calibration

## Refresher: How does calibration work?

{ref}`Calibration <calibration>` involves the step-wise adaptation of model input parameters to yield a possibly best (statistic) fit of modeled and measured data. In the process of model calibration, only one parameter should be modified at a time by 10 to 20-% deviations from its default value. For instance, if the beginning `FRICTION COEFFICIENT : 0.03`, the calibration may test for `FRICTION COEFFICIENT : 0.033`, then `FRICTION COEFFICIENT : 0.036`, `FRICTION COEFFICIENT : 0.027` and so on, ultimately to find out which value for **FRICTION COEFFICIENT** brings the model results closest to observations.

Moreover, a sensitivity analysis compares step-wise modifications of multiple parameters (still: one at a time) and theirs effect on model results. For instance, if a 10-% variation of **FRICTION COEFFICIENT** yields a 5-% change in global water depth while a 10-% variation of grid size (edge length) yields a 20-% change in global water depth, it may be concluded that the model sensitivity is higher with respect to the grid size. However, such conclusions require careful considerations in multi-parametric, complex models of river ecosystems.

## Calibration Parameters in Telemac
The following parameters may be used for calibrating a 2d model to measurements (e.g., water surface elevation, water depth, or flow velocity data):

* **FRICTION COEFFICIENT** ({ref}`friction section <tm2d-friction>`)
* Solvers, solver options, implicitation and other numerical parameters ({ref}`numerical parameter section <tm2d-solver-pars>`)
* Type of model {ref}`initialization <tm2d-init-dry>`
* {ref}`Turbulence models and parameters <tm2d-turbulence>`

```{admonition} Avoid accuracy-reducing keyword settings
Keyword settings such as `MASS-LUMPING ... : ...`  lead to increased smoothing (i.e., reduced accuracy) of results to increase computation speed. However, in most cases, it is worth accepting longer computation times and yielding higher accuracy, which will reduce efforts for model calibration, and thus, saves more time in the end.
```

# Next Steps

1. Make sure the simulation is conservative according to the descriptions in the spotlight chapter on {ref}`mass balance <foc-mass-bc>`.
1. Find a meaningful simulation duration for convergence of a dry-initialized simulation following the algorithms provided with the chapter on {ref}`quantitative convergence <tm-convergence>`.
1. Use the dry-initialized model to simulate at least 2-3 steady discharges (with {ref}`hotstart conditions <tm2d-hotstart>`) for which measurement data is available for {ref}`calibration <calibration>` and validation. 
1. The calibrated and validated model can be 
  * used for {ref}`unsteady hydrodynamic <chpt-unsteady>` simulations, and 
  * serve a basis for morphodynamic {ref}`sediment transport modeling with Gaia <tm-gaia>`.