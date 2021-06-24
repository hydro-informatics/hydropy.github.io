# Geospatial Open Source Python Libraries

This chapter lists open-source packages for geospatial file manipulation with *Python*. The necessary packages are already installed {{ ft_url }}. The following sections provide explanations of relevant and optional packages for this ebook and how those can be installed.

```{admonition} arcpy / ArcGIS
:class: attention
The proprietary license-requiring `arcpy` package is described in the chapter on {ref}`chpt-arcpy`.
```

(gdal)=
## gdal (Includes ogr and osr)

[gdal](https://gdal.org/) for raster data handling, [ogr](https://gdal.org/faq.html?highlight=ogr) for vector data handling, and [osr](https://gdal.org/python/osgeo.osr-module.html) for spatial referencing of the [OSGeo Project](http://www.osgeo.org/) stem from the *GDAL/OGR* project, which is part of the Open Source
Geospatial Foundation ([OSGeo](https://www.osgeo.org) -  the developers of {ref}`qgis-install`). `gdal` provides many methods to convert geospatial data (file types, projections, derive geometries), where `gdal` itself handles {ref}`raster` and its `ogr` module handles {ref}`vector`. The tutorials in this ebook depend on `gdal` and `ogr` (including `osr` for spatial referencing), which is why it is important to get the installation of `gdal` right.

**Linux** users may follow the instructions for installing `gdal` and {{ ft_url }} with {ref}`pip-env`.

**Windows** users preferably install `gdal` and {{ ft_url }} in a {ref}`conda-env`ironment through *Anaconda*.


(geojson-pckg)=
## geojson
The [geojson](https://pypi.org/project/geojson/) library is the most direct option for handling {ref}`geojson` data and is also already installed along with {{ ft_url }}.

````{tabbed} Linux / pip
To install `geojson`, open *Terminal* and type:

```python
pip install geojson
```
````

````{tabbed} Windows / conda
To install `geojson` for *Python Anaconda*, open {ref}`Anaconda Prompt <install-pckg>` and type:

```python
conda install -c conda-forge geojson
```
````

(descartes)=
## Descartes Labs
Even though of proprietary origin, the [`descarteslabs`](https://docs.descarteslabs.com/api.html) package (developed and maintained by [Descartes Labs](https://www.descarteslabs.com/) comes with many open-sourced functions. Moreover, *Descartes Labs* hosts the showcase platform [GeoVisual Search](https://search.descarteslabs.com/) with juicy illustrations of artificial intelligence (AI) applications in geoscience. Note that `descarteslabs` is not installed along with {{ ft_url }}.

````{tabbed} Linux / pip
To install `descarteslabs`, open *Terminal* and type:

```python
pip install descarteslabs
```
````

````{tabbed} Windows / conda
To install `descarteslabs` for *Python*, open {ref}`Anaconda Prompt <install-pckg>` and type:

```python
conda install -c conda-forge descartes
```

If the installation fails, try the following:

```python
conda install shapely
pip install descarteslabs
```
````

## Python Imaging Library (PIL) / pillow
Processing images with *Python* is enabled with the *Python Imaging Library* (*PIL*). *PIL* supports many image file formats and has efficient graphics processing capabilities. The `pillow` library is a user-friendly *PIL* fork and provides `Image*` modules (e.g., `Image`, `ImageDraw`, `ImageMath`, and many more). If {{ ft_url }} is installed, no further action is required for working with the *PIL*/*pillow*-related contents of this ebook.

Note that the `conda base` environment includes `PIL` (test with `import PIL`), which needs to be uninstalled before installing `pillow`. For installing *PIL*/*pillow*, refer to [https://pillow.readthedocs.io](https://pillow.readthedocs.io/en/stable/installation.html).

## shapely

A preferable and very well documented package for {ref}`shp` handling is [`shapely`](https://shapely.readthedocs.io/). `shapely` is already installed along with {{ ft_url }}.

````{tabbed} Linux / pip
To install `shapely`, open *Terminal* and type:

```python
pip install Shapely
```
````

````{tabbed} Windows / conda
To install `shapely` for *Python Anaconda*, open {ref}`Anaconda Prompt <install-pckg>` and type:

```python
conda install -c conda-forge shapely
```
````



## pyshp
Another {ref}`shp` handling package [pyshp](https://pypi.org/project/pyshp/), which provides pure *Python* code (rather than wrappers), which simplifies direct dealing with shapefiles in *Python*. `pyshp` is already installed along with {{ ft_url }}.

````{tabbed} Linux / pip
To install `pyshp`, open *Terminal* and type:

```python
pip install pyshp
```
````

````{tabbed} Windows / conda
To install `pyshp` for *Python Anaconda*, open {ref}`Anaconda Prompt <install-pckg>` and type:

```python
conda install -c conda-forge pyshp
```
````

(other-geo-pckgs)=
## Other packages

Besides the above-mentioned packages, there are other useful libraries for geospatial analyses in *Python* . **`Packages in bold font`** are installed along with {{ ft_url }}.

 * [**`alphashape`**](https://pypi.org/project/alphashape/) creates bounding polygons containing a set of points.
 * [`django`](https://docs.djangoproject.com/en/3.0/ref/contrib/gis/) as a geographic web frame and for database connections.
 * [**`geopandas`**](https://geopandas.org/) enables the application of {ref}`pandas` data frame operations to geospatial datasets.
 * [`NetworkX`](https://networkx.github.io/documentation/stable/index.html) for network analyses such as finding a least-cost / shortest path between two points.
  * [`owslib`](http://geopython.github.io/OWSLib/) to connect with *Open Geospatial Consortium* (OGC) web services.
  * [`postgresql`](https://www.postgresqltutorial.com/postgresql-python/) for SQL database connections.
 * [**`rasterio`**](https://rasterio.readthedocs.io/en/latest/) for processing raster data as {ref}`numpy` arrays.
 * [**`rasterstats`**](https://pythonhosted.org/rasterstats/) produces zonal statistics of rasters and can interact with {ref}`geojson` files.
 * [**`sckit-image`**](https://scikit-image.org/) for machine learning applied to georeferenced images.