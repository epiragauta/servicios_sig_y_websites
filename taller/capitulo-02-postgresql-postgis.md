# Capítulo 2: Instalación de PostgreSQL y PostGIS

## Objetivos del Capítulo

- Comprender qué son PostgreSQL y PostGIS
- Instalar PostgreSQL en Windows
- Instalar la extensión PostGIS
- Crear una base de datos espacial
- Realizar consultas espaciales básicas

## 2.1 ¿Qué es PostgreSQL?

**PostgreSQL** es un sistema de gestión de bases de datos relacional (RDBMS) de código abierto:

### Características Principales

- **Robusto y confiable**: 30+ años de desarrollo activo
- **Cumplimiento ACID**: Garantiza integridad transaccional
- **Extensible**: Soporta extensiones personalizadas
- **Multiplataforma**: Windows, Linux, macOS
- **Estándares SQL**: Cumple con estándares SQL:2016

### ¿Por qué PostgreSQL?

- 🆓 **Gratuito y de código abierto**
- 🚀 **Alto rendimiento**
- 🔒 **Seguro y confiable**
- 🌍 **Soporte para datos espaciales mediante PostGIS**
- 📊 **Amplia adopción en la industria**

## 2.2 ¿Qué es PostGIS?

**PostGIS** es una extensión espacial para PostgreSQL que añade soporte para:

- **Objetos geográficos**: Puntos, líneas, polígonos
- **Consultas espaciales**: Intersecciones, uniones, distancias
- **Índices espaciales**: Consultas rápidas sobre datos geográficos
- **Transformaciones**: Reproyección entre sistemas de coordenadas
- **Análisis geoespacial**: Buffers, centroides, áreas, perímetros

### Capacidades de PostGIS

| Funcionalidad | Descripción |
|---------------|-------------|
| Geometrías | Puntos, LineStrings, Polígonos, Multi-geometrías |
| Geografías | Cálculos sobre el elipsoide terrestre |
| Raster | Almacenamiento y análisis de datos ráster |
| Topología | Relaciones topológicas complejas |
| 3D | Soporte para geometrías tridimensionales |

## 2.3 Instalación de PostgreSQL en Windows

### Paso 1: Descargar PostgreSQL

1. Visita [https://www.postgresql.org/download/windows/](https://www.postgresql.org/download/windows/)
2. Haz clic en **Download the installer**
3. Serás redirigido a EnterpriseDB
4. Descarga la versión más reciente (recomendado: PostgreSQL 16.x)
5. Selecciona la arquitectura de tu sistema (64-bit)

**Enlace directo**: [https://www.enterprisedb.com/downloads/postgres-postgresql-downloads](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)

### Paso 2: Ejecutar el Instalador

1. Ejecuta el archivo descargado (`postgresql-16.x-windows-x64.exe`)
2. Si aparece el Control de Cuentas de Usuario (UAC), haz clic en **Sí**

### Paso 3: Proceso de Instalación

#### Pantalla de Bienvenida
- Haz clic en **Next**

#### Directorio de Instalación
- Por defecto: `C:\Program Files\PostgreSQL\16`
- Puedes dejarlo así o cambiarlo
- Haz clic en **Next**

#### Selección de Componentes
Asegúrate de tener seleccionados:
- ✅ **PostgreSQL Server** (servidor de base de datos)
- ✅ **pgAdmin 4** (herramienta de administración gráfica)
- ✅ **Stack Builder** (para instalar extensiones como PostGIS)
- ✅ **Command Line Tools** (herramientas de línea de comandos)

Haz clic en **Next**

#### Directorio de Datos
- Por defecto: `C:\Program Files\PostgreSQL\16\data`
- Deja el valor por defecto
- Haz clic en **Next**

#### Contraseña del Superusuario
- Establece una contraseña para el usuario `postgres`
- **⚠️ IMPORTANTE**: Recuerda esta contraseña
- Recomendación: Usa una contraseña fuerte pero memorable
- Ejemplo: `postgres123` (solo para desarrollo local)
- Haz clic en **Next**

#### Puerto
- Por defecto: `5432`
- Deja el valor por defecto (a menos que esté ocupado)
- Haz clic en **Next**

#### Configuración Regional (Locale)
- Selecciona tu configuración regional o deja **Default locale**
- Haz clic en **Next**

#### Resumen
- Revisa la configuración
- Haz clic en **Next**

#### Instalación
- Haz clic en **Next** para comenzar
- Espera a que se complete la instalación (puede tardar varios minutos)

#### Stack Builder
- Al finalizar, te preguntará si quieres ejecutar **Stack Builder**
- ✅ **Marca la casilla** (lo usaremos para instalar PostGIS)
- Haz clic en **Finish**

### Paso 4: Verificar la Instalación

#### Verificar el Servicio

1. Presiona `Win + R`
2. Escribe `services.msc` y presiona Enter
3. Busca **postgresql-x64-16**
4. Verifica que el estado sea **Running** (Ejecutándose)

#### Verificar desde CMD

1. Abre **CMD** o **PowerShell**
2. Ejecuta:

```cmd
psql --version
```

Deberías ver:
```
psql (PostgreSQL) 16.x
```

#### Conectarse a PostgreSQL

Desde CMD:
```cmd
psql -U postgres
```

Te pedirá la contraseña. Introdúcela y deberías ver:
```
postgres=#
```

Para salir:
```sql
\q
```

## 2.4 Instalación de PostGIS

### Opción 1: Usando Stack Builder (Recomendado)

#### Paso 1: Abrir Stack Builder

Si no se abrió automáticamente:
1. Busca **Stack Builder** en el menú de inicio
2. Ejecútalo
3. Selecciona tu instalación de PostgreSQL (PostgreSQL 16 on port 5432)
4. Haz clic en **Next**

#### Paso 2: Seleccionar PostGIS

1. Expande **Spatial Extensions**
2. Marca ✅ **PostGIS x.x Bundle for PostgreSQL 16**
3. Haz clic en **Next**

#### Paso 3: Descargar e Instalar

1. Selecciona el directorio de descarga
2. Haz clic en **Next** para descargar
3. Una vez descargado, haz clic en **Next** para instalar
4. Sigue las instrucciones del instalador de PostGIS:
   - Acepta la licencia
   - Deja las opciones por defecto
   - Haz clic en **Next** hasta finalizar

### Opción 2: Descarga Directa

1. Ve a [https://postgis.net/windows_downloads/](https://postgis.net/windows_downloads/)
2. Descarga el instalador correspondiente a tu versión de PostgreSQL
3. Ejecuta el instalador y sigue las instrucciones

### Verificar la Instalación de PostGIS

1. Conéctate a PostgreSQL:
```cmd
psql -U postgres
```

2. Crea una base de datos de prueba:
```sql
CREATE DATABASE test_postgis;
```

3. Conéctate a la base de datos:
```sql
\c test_postgis
```

4. Activa la extensión PostGIS:
```sql
CREATE EXTENSION postgis;
```

5. Verifica la versión:
```sql
SELECT PostGIS_version();
```

Deberías ver algo como:
```
3.4 USE_GEOS=1 USE_PROJ=1 USE_STATS=1
```

6. Verifica las funciones disponibles:
```sql
SELECT count(*) FROM pg_proc WHERE proname LIKE 'st_%';
```

Deberías ver varios cientos de funciones espaciales.

7. Sal de psql:
```sql
\q
```

## 2.5 Crear la Base de Datos para el Taller

### Paso 1: Crear la Base de Datos

Abre **pgAdmin 4** o usa la línea de comandos:

#### Usando pgAdmin 4

1. Abre **pgAdmin 4** desde el menú de inicio
2. Ingresa la contraseña maestra (la que configuraste)
3. Expande **Servers** → **PostgreSQL 16**
4. Click derecho en **Databases** → **Create** → **Database**
5. Nombre: `taller_gis`
6. Owner: `postgres`
7. Haz clic en **Save**

#### Usando la Línea de Comandos

```cmd
psql -U postgres
```

```sql
CREATE DATABASE taller_gis
    WITH
    OWNER = postgres
    ENCODING = 'UTF8'
    LC_COLLATE = 'Spanish_Colombia.1252'
    LC_CTYPE = 'Spanish_Colombia.1252'
    TEMPLATE = template0;
```

### Paso 2: Activar PostGIS

Conéctate a la base de datos:
```sql
\c taller_gis
```

Activa las extensiones necesarias:
```sql
-- Extensión principal de PostGIS
CREATE EXTENSION postgis;

-- Funciones de topología (opcional)
CREATE EXTENSION postgis_topology;

-- Herramientas adicionales (opcional)
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
```

### Paso 3: Verificar la Configuración

Verifica que PostGIS esté activo:
```sql
SELECT postgis_full_version();
```

Lista las extensiones instaladas:
```sql
\dx
```

Deberías ver:
```
List of installed extensions
   Name     | Version |   Schema   |         Description
------------+---------+------------+------------------------------
 postgis    | 3.4.x   | public     | PostGIS geometry and geography...
 plpgsql    | 1.0     | pg_catalog | PL/pgSQL procedural language
```

## 2.6 Herramientas de Administración

### pgAdmin 4

**pgAdmin 4** es la herramienta gráfica principal para administrar PostgreSQL:

#### Características

- 🖥️ Interfaz gráfica intuitiva
- 📊 Visualización de datos y esquemas
- ✏️ Editor SQL con autocompletado
- 📈 Monitor de rendimiento
- 🔧 Herramientas de backup y restore

#### Acceso

1. Abre pgAdmin 4
2. Navega a **Servers** → **PostgreSQL 16** → **Databases** → **taller_gis**
3. Click en **Query Tool** para ejecutar consultas SQL

### psql (Command Line)

**psql** es la herramienta de línea de comandos para PostgreSQL:

#### Comandos Útiles

| Comando | Descripción |
|---------|-------------|
| `\l` | Listar bases de datos |
| `\c database` | Conectar a una base de datos |
| `\dt` | Listar tablas |
| `\d table` | Describir una tabla |
| `\df` | Listar funciones |
| `\dx` | Listar extensiones |
| `\q` | Salir |
| `\?` | Ayuda |
| `\h SQL_COMMAND` | Ayuda sobre comando SQL |

#### Ejemplo de Sesión

```cmd
psql -U postgres -d taller_gis
```

```sql
-- Ver versión de PostgreSQL
SELECT version();

-- Ver versión de PostGIS
SELECT PostGIS_version();

-- Listar tablas
\dt

-- Salir
\q
```

## 2.7 Consultas Espaciales Básicas

### Crear una Tabla con Geometría

```sql
-- Crear una tabla de ciudades
CREATE TABLE ciudades (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100),
    poblacion INTEGER,
    geom GEOMETRY(POINT, 4326)
);
```

**Explicación**:
- `GEOMETRY(POINT, 4326)`: Geometría de tipo POINT con SRID 4326 (WGS84)
- `SRID`: Sistema de referencia espacial (en este caso, coordenadas geográficas)

### Insertar Datos Espaciales

```sql
-- Insertar algunas ciudades de Colombia
INSERT INTO ciudades (nombre, poblacion, geom) VALUES
    ('Bogotá', 7181469, ST_SetSRID(ST_MakePoint(-74.0721, 4.7110), 4326)),
    ('Medellín', 2508452, ST_SetSRID(ST_MakePoint(-75.5636, 6.2442), 4326)),
    ('Cali', 2227642, ST_SetSRID(ST_MakePoint(-76.5225, 3.4516), 4326)),
    ('Barranquilla', 1232766, ST_SetSRID(ST_MakePoint(-74.7813, 10.9685), 4326)),
    ('Cartagena', 1028736, ST_SetSRID(ST_MakePoint(-75.5144, 10.3910), 4326));
```

### Consultas Espaciales

#### Calcular Distancia entre Ciudades

```sql
-- Distancia entre Bogotá y Medellín (en metros)
SELECT
    ST_Distance(
        (SELECT geom FROM ciudades WHERE nombre = 'Bogotá')::geography,
        (SELECT geom FROM ciudades WHERE nombre = 'Medellín')::geography
    ) / 1000 AS distancia_km;
```

#### Encontrar Ciudades Cercanas

```sql
-- Ciudades a menos de 500 km de Bogotá
SELECT
    c2.nombre,
    ST_Distance(c1.geom::geography, c2.geom::geography) / 1000 AS distancia_km
FROM
    ciudades c1,
    ciudades c2
WHERE
    c1.nombre = 'Bogotá'
    AND c2.nombre != 'Bogotá'
    AND ST_Distance(c1.geom::geography, c2.geom::geography) < 500000
ORDER BY distancia_km;
```

#### Crear un Buffer (Área de Influencia)

```sql
-- Crear un buffer de 100 km alrededor de Bogotá
SELECT
    nombre,
    ST_Buffer(geom::geography, 100000)::geometry AS buffer_geom
FROM ciudades
WHERE nombre = 'Bogotá';
```

### Funciones Espaciales Comunes

| Función | Descripción |
|---------|-------------|
| `ST_MakePoint(x, y)` | Crea un punto |
| `ST_SetSRID(geom, srid)` | Asigna un SRID a una geometría |
| `ST_Distance(geom1, geom2)` | Calcula distancia entre geometrías |
| `ST_Area(geom)` | Calcula área de un polígono |
| `ST_Length(geom)` | Calcula longitud de una línea |
| `ST_Buffer(geom, radio)` | Crea buffer alrededor de geometría |
| `ST_Intersects(geom1, geom2)` | Verifica si geometrías se intersecan |
| `ST_Contains(geom1, geom2)` | Verifica si geom1 contiene a geom2 |
| `ST_AsText(geom)` | Convierte geometría a WKT |
| `ST_AsGeoJSON(geom)` | Convierte geometría a GeoJSON |

## 2.8 Sistemas de Referencia Espacial (SRID)

### ¿Qué es un SRID?

Un **SRID** (Spatial Reference System Identifier) identifica un sistema de coordenadas:

- **4326**: WGS84 (coordenadas geográficas: latitud/longitud)
- **3857**: Web Mercator (usado en mapas web como Google Maps)
- **9377**: MAGNA-SIRGAS 2018 Colombia (proyectado)
- **3116**: MAGNA-SIRGAS Colombia Bogotá (proyectado)

### Consultar SRIDs Disponibles

```sql
-- Ver todos los SRIDs
SELECT srid, auth_name, srtext
FROM spatial_ref_sys
WHERE srid IN (4326, 3857, 9377, 3116);
```

### Transformar entre Sistemas de Coordenadas

```sql
-- Transformar de WGS84 (4326) a Web Mercator (3857)
SELECT
    nombre,
    ST_Transform(geom, 3857) AS geom_mercator
FROM ciudades;
```

## 2.9 Índices Espaciales

Los índices espaciales mejoran significativamente el rendimiento de consultas espaciales.

### Crear un Índice Espacial

```sql
-- Crear índice espacial en la columna geom
CREATE INDEX idx_ciudades_geom
ON ciudades
USING GIST (geom);
```

**GIST** (Generalized Search Tree) es el tipo de índice recomendado para datos espaciales.

### Verificar Índices

```sql
-- Listar índices de la tabla
\d ciudades

-- O con SQL
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'ciudades';
```

## Resumen

En este capítulo has aprendido:

✅ Qué son PostgreSQL y PostGIS
✅ Cómo instalar PostgreSQL en Windows
✅ Cómo instalar la extensión PostGIS
✅ Crear una base de datos espacial
✅ Herramientas de administración (pgAdmin 4, psql)
✅ Realizar consultas espaciales básicas
✅ Trabajar con sistemas de referencia espacial
✅ Crear índices espaciales para mejorar rendimiento

## Ejercicio Práctico

1. Crea la base de datos `taller_gis` si no lo has hecho
2. Activa la extensión PostGIS
3. Crea la tabla `ciudades` con los ejemplos del capítulo
4. Inserta los datos de las ciudades
5. Calcula la distancia entre todas las ciudades
6. Crea un buffer de 200 km alrededor de tu ciudad favorita
7. Crea un índice espacial en la tabla

## Referencias

- PostgreSQL Documentation. (2024). *PostgreSQL 16 Documentation*. https://www.postgresql.org/docs/16/
- PostGIS Documentation. (2024). *PostGIS 3.4 Manual*. https://postgis.net/docs/
- Obe, R., & Hsu, L. (2021). *PostGIS in Action* (3rd ed.). Manning Publications.
- Ramsey, P. (2024). *Introduction to PostGIS*. http://postgis.net/workshops/postgis-intro/
- EnterpriseDB. (2024). *PostgreSQL Installer for Windows*. https://www.enterprisedb.com/downloads/postgres-postgresql-downloads

---

**Capítulo anterior**: [Capítulo 1: Introducción a Git](./capitulo-01-git.md)

**Próximo capítulo**: [Capítulo 3: Instalación y Configuración de GeoServer](./capitulo-03-geoserver.md)
