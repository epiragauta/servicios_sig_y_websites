# Capítulo 12: Cloud-native: COG, PMTiles y STAC

> **Capítulo práctico (Módulo 4).** Aquí no solo entiendes los formatos *cloud-native*: los **generas** y publicas. Complementa la parte conceptual del deck M4 del curso.

## Objetivos del Capítulo

- Entender qué hace a un formato *cloud-native* y por qué sirve para la web.
- **Generar** un Cloud-Optimized GeoTIFF (COG) y servirlo con Titiler.
- **Generar** vector tiles en un solo archivo con PMTiles.
- Convertir datos a GeoParquet para analítica.
- Escribir y **validar** un Item STAC mínimo, y consultar una STAC API real.

## Herramientas

Estas herramientas **no vienen en el stack del curso** (PostGIS + GeoServer + pg_tileserv). Instálalas aparte:

| Herramienta | Para qué | Cómo obtenerla |
|---|---|---|
| **GDAL 3.8+** | COG, PMTiles, GeoParquet, GeoJSON | OSGeo4W (Windows), `conda install -c conda-forge gdal`, o contenedor `ghcr.io/osgeo/gdal:ubuntu-small-latest` |
| **Titiler** | servir un COG como teselas | Docker: `ghcr.io/developmentseed/titiler` |
| **stac-validator** | validar STAC | `pip install stac-validator` |

> Con un contenedor GDAL evitas instalar nada en el equipo:
> `docker run --rm -v "%cd%:/data" ghcr.io/osgeo/gdal:ubuntu-small-latest gdalinfo /data/archivo.tif`
> (en PowerShell/cmd usa `%cd%`; en bash, `$PWD`).

---

## 12.1 ¿Qué es "cloud-native"?

Un formato cloud-native está **organizado internamente** para que un cliente lea **solo los bytes que necesita** mediante *HTTP Range requests*, sin descargar el archivo completo y sin un servidor especial: basta con alojarlo en un bucket (S3, Azure Blob) o cualquier HTTP.

Tres ideas:
- **Formato interno** por teselas/bloques + pirámide de resúmenes (*overviews*).
- **Lectura parcial** por rango de bytes.
- **Sin servidor especial**: el archivo se sirve estático.

---

## 12.2 COG — Cloud-Optimized GeoTIFF

Un COG es un GeoTIFF con **teselas internas** y **overviews**, pensado para lectura parcial.

### Generarlo con GDAL

```bash
# Dato sugerido: un recorte del DEM Copernicus 30 m de la zona del Cocuy (Boyacá).
# Fuente abierta (sin credenciales): AWS Open Data "copernicus-dem-30m".
# A partir de cualquier GeoTIFF de entrada:
gdal_translate -of COG entrada.tif cocuy_cog.tif

# Opciones útiles (compresión y remuestreo de overviews):
gdal_translate -of COG -co COMPRESS=DEFLATE -co OVERVIEW_RESAMPLING=AVERAGE entrada.tif cocuy_cog.tif
```

### Verificarlo

```bash
gdalinfo cocuy_cog.tif          # revisa "Block=...x..." (tiled) y los Overviews
rio cogeo validate cocuy_cog.tif   # si tienes rio-cogeo (pip install rio-cogeo)
```

Un COG válido muestra bloques (teselas internas) y varios niveles de *overviews*.

---

## 12.3 Servir el COG con Titiler

Titiler genera teselas **al vuelo** desde un COG (local o remoto).

```bash
docker run --rm -p 8000:8000 ghcr.io/developmentseed/titiler:latest
```

Luego pide teselas o metadatos apuntando a la URL del COG:

```
http://localhost:8000/cog/info?url=https://.../cocuy_cog.tif
http://localhost:8000/cog/tiles/{z}/{x}/{y}?url=https://.../cocuy_cog.tif
```

Esas teselas `z/x/y` se visualizan con **MapLibre** u **OpenLayers** como una capa raster.

> Para que Titiler (en contenedor) lea un COG local, móntalo como volumen y usa una URL `file://` o sírvelo por HTTP; lo habitual es apuntar a un COG en un bucket.

---

## 12.4 PMTiles — vector tiles en un archivo

PMTiles empaqueta una pirámide de **vector tiles** en **un solo archivo**, servible como estático (sin servidor de teselas).

```bash
# Con GDAL 3.8+ (driver PMTiles):
ogr2ogr -f PMTiles municipios.pmtiles municipios_boyaca.geojson

# Alternativas: la CLI 'pmtiles' (Protomaps) o 'tippecanoe' para grandes volúmenes.
```

Se sube a un bucket/CDN y se consume directamente con **MapLibre GL JS** (fuente `type: "vector"` con el protocolo `pmtiles://`). Comparado con un servidor de teselas (pg_tileserv del Capítulo 9), PMTiles no necesita proceso en ejecución: es ideal para datos que cambian poco.

---

## 12.5 GeoParquet — datos columnar

GeoParquet guarda vectores en formato **columnar**, eficiente para analítica y lectura selectiva de columnas.

```bash
ogr2ogr -f Parquet municipios.parquet municipios_boyaca.geojson
```

Útil para procesar grandes volúmenes con herramientas de datos (pandas/GeoPandas, DuckDB) sin cargar geometrías innecesarias.

---

## 12.6 STAC — catalogar los activos

**STAC** (SpatioTemporal Asset Catalog) describe activos geoespaciales (sobre todo ráster) de forma estándar y buscable:

```
Catalog  ──> Collection ──> Item ──> Asset (p. ej. el COG)
```

### Item STAC mínimo (a mano)

Crea `item.json` describiendo el COG del paso 12.2:

```json
{
  "stac_version": "1.0.0",
  "type": "Feature",
  "id": "cocuy-dem-30m",
  "bbox": [-72.5, 6.3, -72.1, 6.6],
  "geometry": {
    "type": "Polygon",
    "coordinates": [[[-72.5,6.3],[-72.1,6.3],[-72.1,6.6],[-72.5,6.6],[-72.5,6.3]]]
  },
  "properties": { "datetime": "2024-01-01T00:00:00Z" },
  "assets": {
    "dem": {
      "href": "https://.../cocuy_cog.tif",
      "type": "image/tiff; application=geotiff; profile=cloud-optimized",
      "roles": ["data"]
    }
  },
  "links": []
}
```

### Validarlo

```bash
pip install stac-validator
stac-validator item.json
```

(Para generarlo automáticamente a partir del COG existe **stactools**: `stac create-item`.)

Explóralo visualmente con **STAC Browser** (radiantearth.github.io/stac-browser).

---

## 12.7 STAC API en vivo

Un catálogo real y abierto para practicar es **Earth Search** (AWS), con Sentinel-2, Landsat y Copernicus DEM:

```
https://earth-search.aws.element84.com/v1/collections
```

Permite búsqueda espacio-temporal (`POST /search` con `bbox` y `datetime`) sin descargar los activos.

---

## 12.8 ¿Cuándo usar cada formato?

| Necesitas… | Formato |
|---|---|
| Ráster grande servido desde la nube | **COG** (+ Titiler) |
| Vector tiles estáticos, sin servidor | **PMTiles** |
| Analítica columnar a escala | **GeoParquet** |
| Streaming vectorial (alternativa a GeoJSON) | **FlatGeobuf** |
| Describir y buscar activos ráster | **STAC** |

---

## Resumen

- *Cloud-native* = formato interno por bloques + overviews + lectura por rango HTTP, sin servidor especial.
- Generaste un **COG** con GDAL y lo serviste con **Titiler**; empaquetaste **PMTiles** y convertiste a **GeoParquet**.
- Escribiste y **validaste** un Item **STAC** que apunta al COG, y consultaste una STAC API real.

---

## Ejercicio Práctico

1. Genera un **COG** a partir de un GeoTIFF (idealmente el DEM del Cocuy) y valídalo (`gdalinfo` muestra teselas + overviews).
2. Genera **`municipios.pmtiles`** desde `municipios_boyaca.geojson` con `ogr2ogr`.
3. Escribe un **Item STAC** (`item.json`) que apunte a tu COG y supéralo con `stac-validator`.

**Entregable:** los archivos `cocuy_cog.tif`, `municipios.pmtiles` e `item.json`, más la captura de `stac-validator` en verde.

---

## Referencias

- Cloud-Optimized GeoTIFF: https://www.cogeo.org/
- PMTiles (Protomaps): https://docs.protomaps.com/pmtiles/
- GeoParquet: https://geoparquet.org/
- STAC Specification: https://stacspec.org/
- Titiler: https://developmentseed.org/titiler/
- Earth Search (STAC API): https://earth-search.aws.element84.com/v1

---

**Capítulo anterior**: [Capítulo 11: Aplicación Web con Leaflet](./capitulo-11-aplicacion-leaflet.md)
