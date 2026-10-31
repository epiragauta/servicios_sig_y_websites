# Capítulo 10: OGC API Features con pygeoapi

## Objetivos del Capítulo

- Entender **OGC API - Features** como la generación moderna (REST + JSON) de los servicios de datos vectoriales.
- Instalar y levantar **pygeoapi** con Docker.
- Configurar un *provider* **PostgreSQL/PostGIS** apuntando a la base `taller_gis`.
- Publicar las tablas `departamentos` y `municipios` como colecciones OGC API - Features.
- Consultar las colecciones (`/collections`, `/items`, filtros) y preparar el consumo desde la aplicación web.

---

## 10.1 De consumir a publicar OGC API

En el **Capítulo 2** consumiste servicios OGC que ya existían (WMS, WFS y OGC API ajenas). En el **Capítulo 8** publicaste tus datos con GeoServer (WMS/WFS clásicos). Ahora cierras el panorama publicando tus propios datos con un estándar **moderno**: **OGC API - Features**.

A diferencia del WFS clásico (peticiones `GetFeature` con parámetros KVP y respuestas GML), OGC API - Features es una **API REST** con rutas predecibles y respuestas en **GeoJSON**, pensada para la web:

```
GET /collections                      -> lista de colecciones
GET /collections/departamentos        -> metadatos de la colección
GET /collections/departamentos/items  -> features en GeoJSON
```

---

## 10.2 ¿Qué es pygeoapi?

**pygeoapi** es una implementación en **Python** de las normas **OGC API** (Features, Coverages, Tiles, Records, Processes…). Sus ideas clave:

- Se configura con **un archivo YAML** (`pygeoapi-config.yml`): no se programa, se declara.
- Cada capa publicada es un **provider**. Hay providers para PostgreSQL/PostGIS, GeoPackage, CSV, Elasticsearch, etc.
- Genera automáticamente la documentación **OpenAPI** y una interfaz HTML navegable.

> En este capítulo usamos el provider **PostgreSQL** para publicar las tablas que cargaste en el Capítulo 7. El curso incluye además un taller detallado de pygeoapi con GeoPackage en `03_Material_Clase/Talleres/Servicios_SIG_con_Python/`.

---

## 10.3 Levantar pygeoapi con Docker

La imagen oficial es `geopython/pygeoapi`. Basta montar nuestro `pygeoapi-config.yml` dentro del contenedor y exponer el puerto `5000`.

### Opción A: añadir el servicio al `docker-compose.yml` (recomendado)

Así pygeoapi comparte la red de Docker con PostGIS y lo alcanza por su nombre de servicio:

```yaml
  pygeoapi:
    image: geopython/pygeoapi:latest
    container_name: curso_pygeoapi
    ports:
      - "5000:80"
    volumes:
      - ./pygeoapi/pygeoapi-config.yml:/pygeoapi/local.config.yml
    depends_on:
      postgis:
        condition: service_healthy
```

> La imagen sirve en el puerto **80** dentro del contenedor; lo publicamos como **5000** en el host. Monta tu configuración sobre `/pygeoapi/local.config.yml` (la que la imagen usa por defecto).

### Opción B: `docker run` puntual

```bash
docker run --rm -p 5000:80 \
  -v "$PWD/pygeoapi/pygeoapi-config.yml:/pygeoapi/local.config.yml" \
  --network servicios_sig_y_websites_default \
  geopython/pygeoapi:latest
```

Crea la carpeta `pygeoapi/` y, dentro, el archivo de configuración de la siguiente sección.

---

## 10.4 Configuración base y provider PostGIS

Crea `pygeoapi/pygeoapi-config.yml`. Empieza por el bloque `server` y `metadata`:

```yaml
server:
    bind:
        host: 0.0.0.0
        port: 80
    url: http://localhost:5000
    mimetype: application/json; charset=UTF-8
    encoding: utf-8
    languages: [es, en]
    cors: true
    pretty_print: true
    limit: 10
    map:
        url: https://tile.openstreetmap.org/{z}/{x}/{y}.png
        attribution: '&copy; OpenStreetMap contributors'

logging:
    level: INFO

metadata:
    identification:
        title: Servicios SIG - Boyacá (curso)
        description: OGC API - Features sobre PostGIS
        keywords: [boyacá, colombia, ogc api, postgis]
    license:
        name: CC-BY 4.0
        url: https://creativecommons.org/licenses/by/4.0/
    provider:
        name: Maestría SIG - UJdeC
        url: http://localhost:5000
    contact:
        name: Equipo del curso
        email: contacto@ejemplo.com

resources:
    departamentos:
        type: collection
        title: Departamentos
        description: División administrativa de primer nivel
        keywords: [departamentos, colombia]
        extents:
            spatial:
                bbox: [-79.0, -4.2, -66.8, 13.4]
                crs: http://www.opengis.net/def/crs/OGC/1.3/CRS84
        providers:
            - type: feature
              name: PostgreSQL
              data:
                  host: postgis        # ver nota sobre el host
                  port: 5432
                  dbname: taller_gis
                  user: postgres
                  password: postgres
                  search_path: [public]
              table: departamentos
              id_field: gid
              geom_field: geom
```

> **El host depende de dónde corra pygeoapi:**
> - Si pygeoapi corre **en Docker en la misma red** del `docker-compose` (Opción A/B), el host es **`postgis`** (el nombre del servicio).
> - Si corre **fuera** (instalado con `pip` en tu equipo), el host es **`localhost`** (porque el puerto 5432 está publicado).

Para publicar también `municipios`, duplica el bloque de recurso cambiando `table: municipios`, el `id_field` si difiere y el título.

---

## 10.5 Arrancar y verificar

Levanta el servicio (`docker compose up -d pygeoapi`) y comprueba en el navegador o con `curl`:

```bash
# Página principal e interfaz HTML
curl http://localhost:5000/
# http://localhost:5000/openapi?f=html   (documentación interactiva)

# Lista de colecciones
curl http://localhost:5000/collections?f=json

# Metadatos de la colección
curl http://localhost:5000/collections/departamentos

# Features en GeoJSON (las 10 primeras)
curl "http://localhost:5000/collections/departamentos/items?f=json&limit=10"
```

Parámetros útiles de `/items`:

| Parámetro | Para qué | Ejemplo |
|-----------|----------|---------|
| `limit` / `offset` | paginación | `?limit=50&offset=50` |
| `bbox` | filtro espacial | `?bbox=-74.5,5.1,-72.0,6.9` |
| `<campo>=<valor>` | filtro por atributo | `?dpto_cnmbr=BOYACÁ` |
| `filter` (CQL2) | filtro avanzado | `?filter=dpto_ccdgo='15'` |
| `f` | formato | `?f=json` (GeoJSON) · `?f=html` |

---

## 10.6 Instalación nativa (opcional)

Si no puedes usar Docker, pygeoapi se instala con `pip` (requiere Python 3.8+ y GDAL):

```bash
python -m venv venv
source venv/bin/activate          # Windows PowerShell: .\venv\Scripts\Activate.ps1
pip install pygeoapi psycopg2-binary
export PYGEOAPI_CONFIG=pygeoapi/pygeoapi-config.yml
export PYGEOAPI_OPENAPI=pygeoapi/openapi.yml
pygeoapi openapi generate $PYGEOAPI_CONFIG > $PYGEOAPI_OPENAPI
pygeoapi serve
```

En instalación nativa, el `host` del provider es `localhost`. El paso a paso completo (con GeoPackage y ramas por paso) está en `03_Material_Clase/Talleres/Servicios_SIG_con_Python/`.

---

## 10.7 pygeoapi frente a otras formas de servir vectores

| | FeatureServer de Esri (Cap. 2) | WFS con GeoServer (Cap. 8) | OGC API - Features con pygeoapi |
|---|---|---|---|
| Estilo | REST propietario | KVP / XML (estándar OGC clásico) | REST estándar (OGC API) |
| Formato típico | JSON/GeoJSON | GML (o GeoJSON) | **GeoJSON** |
| Ruta de datos | `/query?...` | `?request=GetFeature` | `/collections/{id}/items` |
| Documentación | ArcGIS REST | `GetCapabilities` | **OpenAPI** |

Es el **mismo dato**, servido con distintas gramáticas. OGC API - Features es la más amigable para la web moderna.

---

## 10.8 Puente a la aplicación web

En el **Capítulo 11** la aplicación consumirá estas colecciones directamente con `fetch`, aprovechando que la respuesta ya es **GeoJSON** —el formato nativo de Leaflet y MapLibre—:

```js
fetch("http://localhost:5000/collections/departamentos/items?f=json&limit=50")
  .then(r => r.json())
  .then(geojson => L.geoJSON(geojson).addTo(mapa));
```

---

## Resumen

- OGC API - Features expone datos vectoriales por **REST + GeoJSON**, con rutas predecibles (`/collections/.../items`).
- **pygeoapi** publica PostGIS declarando un *provider* en un **YAML**, sin programar.
- El **host** del provider es `postgis` (pygeoapi en Docker) o `localhost` (pygeoapi nativo).
- La respuesta GeoJSON se consume directamente desde el cliente web del Capítulo 11.

---

## Ejercicio Práctico

1. Levanta pygeoapi con Docker y publica `departamentos` **y** `municipios` como colecciones.
2. Verifica `http://localhost:5000/collections` (deben aparecer las dos).
3. Consulta `http://localhost:5000/collections/municipios/items?f=json&limit=5` y copia el primer `Feature`.
4. Aplica un filtro: trae solo los municipios de Boyacá con `?filter=dpto_ccdgo='15'` (o el campo equivalente) y anota cuántos obtienes.

**Entrega:** la URL de tu colección `municipios`, una captura de `/items` en formato HTML y el conteo de features filtradas.

---

## Referencias

- Documentación oficial de pygeoapi: https://docs.pygeoapi.io
- OGC API - Features: https://ogcapi.ogc.org/features/
- Provider PostgreSQL de pygeoapi: https://docs.pygeoapi.io/en/latest/data-publishing/ogcapi-features.html
- Taller de pygeoapi del curso: `03_Material_Clase/Talleres/Servicios_SIG_con_Python/`

---

**Capítulo anterior**: [Capítulo 9: Vector tiles con pg_tileserv](./capitulo-09-pg-tileserv.md)

**Próximo capítulo**: [Capítulo 11: Aplicación Web con Leaflet](./capitulo-11-aplicacion-leaflet.md)
