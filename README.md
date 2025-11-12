# Curso de Servicios Web Geográficos

Repositorio del curso práctico sobre servicios web geográficos (WMS y WFS), estándares OGC y desarrollo de aplicaciones web cartográficas.

## Contenido

Este repositorio contiene:

- **Datos geográficos** de Colombia (departamentos y municipios)
- **Curso completo** por capítulos en formato Markdown
- **Aplicación web** de ejemplo con Leaflet.js
- **Archivos SLD** para estilos de capas

## Objetivo

Aprender a configurar, administrar y consumir servicios web geográficos utilizando tecnologías estándar OGC (Open Geospatial Consortium), con énfasis en WMS y WFS.

## Curso

El curso está organizado en 8 capítulos:

1. [Introducción a Git](./taller/capitulo-01-git.md)
2. [Instalación de PostgreSQL y PostGIS](./taller/capitulo-02-postgresql-postgis.md)
3. [Instalación y Configuración de GeoServer](./taller/capitulo-03-geoserver.md)
4. [Servicios Web Geográficos (WMS, WFS)](./taller/capitulo-04-servicios-web-geograficos.md)
5. [Estándares para Información Geográfica (GeoJSON, SLD)](./taller/capitulo-05-estandares-geograficos.md)
6. [Carga de Datos a PostgreSQL](./taller/capitulo-06-carga-datos-postgresql.md)
7. [Publicación de Capas en GeoServer](./taller/capitulo-07-publicacion-geoserver.md)
8. [Aplicación Web con Leaflet](./taller/capitulo-08-aplicacion-leaflet.md)

**Comienza aquí**: [Índice del Curso](./taller/README.md)

## Datos

El directorio `data/` contiene:

- **departamentos.shp** - Shapefile de departamentos de Colombia
- **municipios.shp** - Shapefile de municipios de Colombia
- **departamentos.geojson** - GeoJSON de departamentos
- **municipios.geojson** - GeoJSON de municipios
- **departamentos.sld** - Estilo SLD para departamentos
- **municipios.sld** - Estilo SLD para municipios

## Aplicación Web

La carpeta `webapp/` contiene una aplicación web completa que:

- Consume servicios WMS y WFS de GeoServer
- Visualiza departamentos y municipios de Colombia
- Permite búsqueda interactiva
- Incluye múltiples capas base
- Es responsive (funciona en móvil y desktop)

**Ver**: [Documentación de la Aplicación](./webapp/README.md)

## Inicio Rápido

### 1. Clonar el Repositorio

```bash
git clone https://github.com/usuario/servicios_sig_y_websites.git
cd servicios_sig_y_websites
```

### 2. Seguir el Curso

Lee el [Índice del Curso](./taller/README.md) y sigue los capítulos en orden.

### 3. Ejecutar la Aplicación

```bash
cd webapp
python -m http.server 8000
```

Abre [http://localhost:8000](http://localhost:8000)

## Requisitos

- **Git** - Control de versiones
- **PostgreSQL 16+** con **PostGIS 3.4+**
- **GeoServer 2.24+**
- **Java 11 o 17** (para GeoServer)
- **Navegador web moderno**

## Tecnologías Utilizadas

- **PostgreSQL/PostGIS** - Base de datos espacial
- **GeoServer** - Servidor de mapas OGC
- **Leaflet.js** - Biblioteca de mapas web
- **Estándares OGC** - WMS, WFS, SLD

## Aprendizajes

Al completar este curso, serás capaz de:

- Instalar y configurar PostgreSQL con PostGIS
- Instalar y configurar GeoServer
- Cargar datos geográficos a PostgreSQL
- Publicar capas mediante servicios WMS y WFS
- Aplicar estilos SLD personalizados
- Consumir servicios OGC desde aplicaciones web
- Desarrollar aplicaciones web cartográficas con Leaflet

## Licencia

Este material está disponible para fines educativos.

## Autor

Curso de Servicios Web Geográficos - 2024

## Contribuciones

Las contribuciones, correcciones y mejoras son bienvenidas. Por favor abre un issue o pull request.

---

**¡Comienza ahora!** → [Capítulo 1: Introducción a Git](./taller/capitulo-01-git.md)