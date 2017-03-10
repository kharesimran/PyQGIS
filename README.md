# PyQGIS
For the Python and QGIS Workshop at HSR on 10th Mar 2017. 

## Introduction to PyQGIS
  * QGIS supports scripting in Python
  * Allows you to build plugins to extend the core functionality of QGIS and write scripts to automate tasks
  * Python preferred over C++ plugins because of its simplicity of distribution and easier development

## Setting up your development environment
 To get started with PyQGIS, the following are required:
  * QGIS 2.x (We will be using QGIS 2.18)
  * Python 2.x (We will be using Python 2.7)
  * Qt
  * PyQt
  
Downloading the standalone or OSGeo4W installer will automatically install the correct version of Python as well as Qt and PyQt. (Link: [Download QGIS](http://www.qgis.org/en/site/forusers/download.html)) 
  
## Getting some sample data

* *The PyQGIS Programmer's Guide's* [download page](http://locatepress.com/ppg/data_code)
* For raster layers, we can download one of the [Natural Earth rasters](http://www.naturalearthdata.com/downloads/)
* In today's example we will use the [Airports dataset](http://www.naturalearthdata.com/downloads/10m-cultural-vectors/airports/) and [Populated places dataset](http://www.naturalearthdata.com/downloads/10m-cultural-vectors/10m-populated-places/) retreived from Natural Earth 

## Using the QGIS Python Console
  * With QGIS running, open the console using `Plugins -> Python Console` or by clicking on the `Python console` button on the `Plugin toolbar`
  * The toolbar contains the tools *Clear console, Import class, Run command, Show editor, Settings,* and *Help*
  * The built-in code editor can be used along with the console
  * The QGIS API offers a large number of [Python classes](http://labs.webgeodatavore.com/partage/diagramme_principal.html) that we can use. See [Searchable documentation of PyQGIS classes](http://geoapis.sourcepole.com/qgispyapi/qgsnetworkaccessmanager)
  * For the convenience of the user, the following statements are executed when the console is started  
```python
from qgis.core import *
import qgis.utils
```

## Example

In this example we will load a vector point layer of the major airports in the world and write a Python script to print out the name, code, latitude and longitude of each airport. To accomplish this, we:

**Load the Vector Layer**

We can load a vector layer in two ways:
 1. Layer -> Add Layer -> Add Vector Layer -> Browse to the directory and select the shapefile
 2. Use the Python console to open and display the vector layer
```python
layer = iface.addVectorLayer('path_to_shapefile', 'layer_name', 'ogr')
if not layer:
    print "Layer failed to load"
```

**Get a reference to the currently active layer**
```python
layer = iface.activeLayer()
```

**Print out the `name` and `iata_code` of each airport feature**
```python
for f in layer.getFeatures():
    print f['name'], f['iata_code']
```

**Get the geometry of each feature and print out its `latitude (y)` and `longitude (x)`**
```python
for f in layer.getFeatures():
    geom = f.geometry()
    print 'lat %.3f, lon %.3f' % (geom.asPoint().y(), geom.asPoint().x())
```

**Python script to output to a text file the `name`, `iata_code`, `latitude` and `longitude` of each airport in the northern hemisphere**
```python
output_file = open('//svm-c113.hsr.ch/skhare/Desktop/PyQGIS/pyqgis_output.txt', 'w')
for f in layer.getFeatures():
  geom = f.geometry()
  # For all airports in the northern hemisphere, latitude (y coordinate) > 0
  if geom.asPoint().y() > 0:
    line = '%s, %s, %f, %f\n' % (f['name'], f['iata_code'],
          geom.asPoint().y(), geom.asPoint().x())
    unicode_line = line.encode('utf-8')
    output_file.write(unicode_line)
output_file.close()
```

## Expressions in QGIS and Custom Python Expression Functions

**Select using QGIS Expression**
 * Load the `populated places` shapefile
 * Go to View -> Select -> Select by Expression
 * Select all the features (populated places) lying within a country `sov0name`
 
**Overview of Custom Python Expression Functions**
 * QGIS allows you to write user-defined functions in Python for selecting, calculating field values and labelling features
 * Write Python code using the Function Editor and execute it in the Expression engine
 * More examples can be found at [QGIS Tutorials and Tips](http://www.qgistutorials.com/en/docs/custom_python_functions.html) and [User Defined Expression Function for QGIS](https://nathanw.net/2012/11/10/user-defined-expression-functions-for-qgis/)


## Resources and references

* *The PyGIS Programmer's Guide - Gary Sherman*
* [PyQGIS Developer Cookbook - Official docs](http://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/)
* [QGIS Tutorials](http://www.qgistutorials.com/en/)
