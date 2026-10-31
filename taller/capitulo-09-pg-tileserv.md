# Capítulo 9: Vector tiles con pg_tileserv

En el capítulo anterior publicaste tus capas como **WMS/WFS** con GeoServer. Ahora las vas a servir como **teselas vectoriales (vector tiles)** directamente desde PostGIS con **pg_tileserv**, un microservicio ligero de Crunchy Data. Es el formato ideal para mapas web fluidos con muchos datos.

## Objetivos del Capítulo

- Entender qué son los **vector tiles** y el formato **MVT**.
- Publicar tablas de PostGIS como teselas vectoriales con **pg_tileserv**, sin configuración manual.
- Descubrir y consumir las capas (TileJSON, URL de teselas `{z}/{x}/{y}.pbf`).
- Distinguir cuándo conviene **pg_tileserv** frente a **GeoServer (WMS/WFS)**.

## 9.1 ¿Qué son los vector tiles (MVT)?

Un **vector tile** es una tesela que, en lugar de una imagen, contiene **geometría vectorial** (puntos, líneas, polígonos) y sus atributos, troceada en la misma pirámide `z/x/y` que viste en el Capítulo 2. El formato estándar es **MVT** (*Mapbox Vector Tile*), codificado en Protobuf (archivos `.pbf`).

| Enfoque | Qué viaja al cliente | Estilo | Ideal para |
|---------|----------------------|--------|------------|
| **WMS / WMTS** | Imagen ya dibujada (PNG) | En el servidor (SLD) | Capas pesadas, impresión |
| **WFS / GeoJSON** | Todos los datos de una vez | En el cliente | Pocos cientos de objetos |
| **Vector tiles (MVT)** | Solo la geometría del área visible, por tesela | En el **cliente** | Muchos datos, zoom/pan fluido |

La ventaja: el navegador recibe **solo lo visible**, lo renderiza con la GPU y permite estilizar e interactuar sin volver a pedir datos.

## 9.2 Levantar y verificar pg_tileserv

pg_tileserv ya está definido en el `docker-compose.yml` del curso (servicio `pg_tileserv`, conectado a la base `taller_gis`). Solo hay que levantarlo:

```bash
docker compose up -d
docker compose ps        # el servicio pg_tileserv debe estar "running"
```

Verifica que responde:

- Interfaz web: [http://localhost:7800/](http://localhost:7800/)
- Índice de capas en JSON: [http://localhost:7800/index.json](http://localhost:7800/index.json)

> pg_tileserv se conecta a PostGIS por el nombre de servicio `postgis` (ver `DATABASE_URL` en el `docker-compose.yml`), igual que GeoServer en el Capítulo 8.

## 9.3 Descubrir las capas publicadas

pg_tileserv **publica automáticamente** todas las tablas con geometría de la base. Como en el Capítulo 7 cargaste `departamentos` y `municipios` en el esquema `public`, aparecerán como:

- `public.departamentos`
- `public.municipios`

Cada capa tiene tres recursos útiles:

| Recurso | URL | Para qué |
|---------|-----|----------|
| Página de detalle / visor | `http://localhost:7800/public.municipios.html` | Previsualizar sobre un mapa |
| **TileJSON** | `http://localhost:7800/public.municipios.json` | Metadatos: extent, zooms, plantilla de teselas |
| **Teselas MVT** | `http://localhost:7800/public.municipios/{z}/{x}/{y}.pbf` | Las teselas que consume el cliente |

Abre el `index.json`: verás la lista de capas detectadas. Si acabas de cargar una tabla y no aparece, recarga la página (pg_tileserv refresca su catálogo).

## 9.4 La URL de teselas

La plantilla de teselas sigue el esquema `z/x/y` del Capítulo 2, pero con extensión `.pbf` (MVT):

```
http://localhost:7800/public.municipios/{z}/{x}/{y}.pbf
```

- `z` = nivel de zoom, `x`/`y` = columna/fila de la tesela.
- `.pbf` = *Protobuf*; **no** es una imagen: es geometría que el cliente dibuja.

Si abres una tesela concreta en el navegador (p. ej. `.../public.municipios/6/18/30.pbf`) se **descargará** un binario: es normal, está pensado para que lo lea una librería de mapas, no el ojo humano.

## 9.5 Previsualizar la capa

La forma rápida de ver el resultado es la **página de la capa**:

```
http://localhost:7800/public.municipios.html
```

Muestra las teselas de `municipios` sobre un mapa base, con la vista centrada en los datos. Haz zoom y desplázate: observa que solo se piden las teselas del área visible (puedes verlo en *DevTools → Network*, como en el Capítulo 2).

## 9.6 Parámetros útiles

pg_tileserv permite ajustar lo que entrega cada capa mediante *query params* en la URL de teselas:

- `properties=` — limita las columnas de atributos incluidas (teselas más livianas). Ej.: `?properties=mpio_cnmbr,mpio_ccdgo`.
- `filter=` — filtra por atributo con una condición tipo SQL. Ej.: `?filter=dpto_ccdgo='15'` (solo Boyacá).

```
http://localhost:7800/public.municipios/{z}/{x}/{y}.pbf?properties=mpio_cnmbr&filter=dpto_ccdgo='15'
```

> **Avanzado (opcional):** además de tablas, pg_tileserv puede publicar **funciones** de PostgreSQL (*function layers*) para teselas dinámicas o parametrizadas. Queda como tema de profundización.

## 9.7 pg_tileserv frente a GeoServer

No compiten: se complementan. Elige según el caso:

| Necesitas… | Usa |
|---|---|
| Teselas vectoriales rápidas desde PostGIS, estilizadas en el cliente | **pg_tileserv** |
| WMS/WFS estándar, estilos SLD server-side, formatos clásicos | **GeoServer** |
| Muchísimos objetos con zoom/pan fluido | **pg_tileserv** (MVT) |
| Imagen renderizada o interoperar con clientes OGC clásicos | **GeoServer** (WMS) |

## 9.8 Puente hacia la aplicación web

Las teselas MVT se consumen de forma natural con **MapLibre GL JS** (que verás en el Módulo 5) o, en Leaflet, con el plugin **Leaflet.VectorGrid**. En el **Capítulo 11** integraremos en una sola app las tres fuentes: WMS/WFS de GeoServer, vector tiles de pg_tileserv y OGC API Features de pygeoapi.

## Resumen

- Los **vector tiles (MVT)** envían geometría por tesela; el cliente los estiliza y renderiza.
- **pg_tileserv** publica automáticamente las tablas de PostGIS como teselas, sin configuración por capa.
- Cada capa expone su **TileJSON** y una plantilla `{z}/{x}/{y}.pbf`, filtrable con `properties=` y `filter=`.
- **pg_tileserv** (vector tiles) y **GeoServer** (WMS/WFS) resuelven necesidades distintas.

## Ejercicio Práctico

1. Levanta el stack (`docker compose up -d`) y abre [http://localhost:7800/](http://localhost:7800/).
2. Entra a la página de `public.municipios` y previsualiza las teselas.
3. Anota la **URL de teselas** (`.../public.municipios/{z}/{x}/{y}.pbf`) y toma una **captura** del visor.
4. **Opcional:** aplica `?filter=dpto_ccdgo='15'` para mostrar solo los municipios de Boyacá y compáralo.

**Entregable:** la URL de teselas de tu capa + captura del visor (y, si hiciste el opcional, la URL filtrada).

## Referencias

- pg_tileserv (Crunchy Data): https://access.crunchydata.com/documentation/pg_tileserv/latest/
- Especificación Mapbox Vector Tile (MVT): https://github.com/mapbox/vector-tile-spec
- Esquema de teselas `z/x/y`: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames

---

**Capítulo anterior**: [Capítulo 8: Publicación de Capas en GeoServer](./capitulo-08-publicacion-geoserver.md)

**Próximo capítulo**: [Capítulo 10: OGC API Features con pygeoapi](./capitulo-10-pygeoapi.md)
