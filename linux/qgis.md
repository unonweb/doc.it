# Install

* https://www.qgis.org/resources/installation-guide/

```sh
sudo apt install gnupg software-properties-common
sudo wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
sudo nano /etc/apt/sources.list.d/qgis.sources

Types: deb deb-src
URIs: https://qgis.org/debian
Suites: <your-distributions-codename> # . /etc/os-release; echo "$UBUNTU_CODENAME"
Architectures: amd64
Components: main
Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg

sudo apt update
sudo apt install qgis qgis-plugin-grass
```

# OpenStreetMap-Daten in QGIS nutzen

## Download

* https://download.geofabrik.de/
* https://download.bbbike.org/osm/

## Doku

* https://wiki.openstreetmap.org/wiki/Planet.osm#Country_and_area_extracts

## How To

* https://www.youtube.com/watch?v=oqFRtiCkCyw
* https://www.dirkkoller.de/openstreetmap-daten-in-qgis-nutzen

*When using QGIS, you only want to import the absolutely smallest amount  of data you can get away with if you want the application to remain  responsive.*

Features drawn as area in OpenStreetMap will be written to the layer with an **_a** **suffix**. Features drawn als line/point in OpenStreetMap will be written to the layer without an **_a** **suffix**.

### Format

QGIS und auch das im Folgenden vorgestellte Werkzeug zum Extrahieren von Teildaten kann mit der Binärvariante (**osm.pbf**) der Daten umgehen. Es  empfiehlt sich also, zum Nachvollziehen eine Datei dieses Formats zu  laden. Die gepackte XML-Variante **osm.bz2** ist etwas größer und für  Anwendungen gedacht, die das Binärformat nicht direkt unterstützen.  Darüber hinaus steht für ArcGis-Anwender noch eine Shapefile-Variante  (**shp.zip**) zur Verfügung.

# PLUGINS

* **QuickMapServices**

## QuickOSM

* https://www.youtube.com/watch?v=My0EeXbVayE

  

# TILES

## xyz

```sh
# OpenTopoMap
https://tile.opentopomap.org/{z}/{x}/{y}.png

# OpenStreetMap
http://tile.openstreetmap.org/{z}/{x}/{y}.png

# Google Hybrid
https://mt1.google.com/vt/lyrs=y&x={x}&y={y}&z={z}

# Google Satellite
https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}

# Google Road
https://mt1.google.com/vt/lyrs=m&x={x}&y={y}&z={z}

# Bing Aerial
http://ecn.t3.tiles.virtualearth.net/tiles/a{q}.jpeg?g=1
```

* See [comment below for attribution](https://gis.stackexchange.com/questions/20191/adding-basemaps-from-google-or-bing-in-qgis/217670?noredirect=1#comment542255_217670)
* Codes for other tile types from Google [found here](https://stackoverflow.com/a/33023651/1704448)

# Elevation

## DEM

* https://asf.alaska.edu/how-to/data-basics/asf-services-data-discovery/
* https://opentopography.org/blog/introducing-api-keys-access-opentopography-global-datasets

# Layers

## Temporary Scratch Layers

Temporary Scratch Layers are in-memory layers, meaning that they are not saved on disk and will be discarded when QGIS is closed. They can be handy for storing features you temporarily need or as intermediate layers during geoprocessing operations.

## properties

## display

### show thumbnail image

```html
<img src="file:///[% photo %]" width="350" height="250">
```

# Python

**Check QGIS Python Environment**:

- Go to `Plugins` > `Python Console` in QGIS.

- Type the following command to check which Python environment QGIS is using:

  ```python
  import sys
  print(sys.executable)
  ```

**Use the QGIS Python Console**:

- To install packages directly into QGIS’s Python environment, you can use the QGIS Python Console:

  ```
  import pip
  pip.main(['install', 'PyQt5', 'PyQtWebEngine'])
  ```
