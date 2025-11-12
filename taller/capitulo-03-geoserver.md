# Capítulo 3: Instalación y Configuración de GeoServer

## Objetivos del Capítulo

- Comprender qué es GeoServer y su propósito
- Instalar Java (requisito previo)
- Instalar GeoServer en Windows
- Configurar GeoServer
- Familiarizarse con la interfaz de administración

## 3.1 ¿Qué es GeoServer?

**GeoServer** es un servidor de código abierto para compartir datos geoespaciales:

### Características Principales

- 🌐 **Servidor de mapas**: Publica mapas a través de estándares OGC
- 📊 **Múltiples fuentes de datos**: Shapefiles, PostGIS, Oracle, MySQL, etc.
- 🎨 **Estilos personalizables**: Mediante SLD y CSS
- 🔌 **Extensible**: Arquitectura de plugins
- 📡 **APIs REST**: Administración programática
- 🔒 **Seguridad**: Control de acceso granular

### Servicios Soportados

| Servicio | Descripción |
|----------|-------------|
| **WMS** | Web Map Service - Mapas como imágenes |
| **WFS** | Web Feature Service - Datos vectoriales |
| **WCS** | Web Coverage Service - Datos ráster |
| **WPS** | Web Processing Service - Geoprocesamiento |
| **WMTS** | Web Map Tile Service - Mapas en teselas |

### ¿Por qué GeoServer?

- - **Código abierto** y gratuito
- - **Cumplimiento de estándares** OGC
- - **Ampliamente adoptado** en la industria
- - **Comunidad activa** y documentación extensa
- - **Integración con PostGIS** y otras bases de datos
- - **Alto rendimiento** y escalabilidad

## 3.2 Requisitos del Sistema

### Requisitos Mínimos

- **Sistema Operativo**: Windows 7/10/11 (64-bit)
- **RAM**: 2 GB (4 GB recomendado)
- **Disco**: 500 MB para GeoServer + espacio para datos
- **Java**: JRE 11 o superior

### Requisitos Recomendados

- **RAM**: 4-8 GB
- **CPU**: 2+ núcleos
- **Disco**: SSD para mejor rendimiento
- **Java**: OpenJDK 11 o 17

## 3.3 Instalación de Java

GeoServer requiere Java Runtime Environment (JRE) o Java Development Kit (JDK).

### Opción 1: Descargar OpenJDK (Recomendado)

#### Usando Adoptium (Eclipse Temurin)

1. Ve a [https://adoptium.net/](https://adoptium.net/)
2. Haz clic en **Download**
3. Selecciona:
   - **Version**: 17 (LTS)
   - **Operating System**: Windows
   - **Architecture**: x64
   - **Package Type**: JRE o JDK
4. Descarga el archivo `.msi`

#### Instalación

1. Ejecuta el archivo descargado (`OpenJDK17-jre_x64_windows.msi`)
2. Acepta la licencia
3. En **Custom Setup**:
   - - **Add to PATH** (importante)
   - - **JAVA_HOME variable**
   - - **JavaSoft (Oracle) registry keys**
4. Haz clic en **Next** e **Install**
5. Finaliza la instalación

### Opción 2: Descargar Oracle JDK

1. Ve a [https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/)
2. Descarga JDK 17 para Windows
3. Ejecuta el instalador y sigue las instrucciones

### Verificar la Instalación de Java

Abre **CMD** o **PowerShell** y ejecuta:

```cmd
java -version
```

Deberías ver algo como:
```
openjdk version "17.0.9" 2023-10-17
OpenJDK Runtime Environment Temurin-17.0.9+9 (build 17.0.9+9)
OpenJDK 64-Bit Server VM Temurin-17.0.9+9 (build 17.0.9+9, mixed mode, sharing)
```

Verifica la variable de entorno JAVA_HOME:

```cmd
echo %JAVA_HOME%
```

Debería mostrar algo como:
```
C:\Program Files\Eclipse Adoptium\jre-17.0.9.9-hotspot
```

### Configurar JAVA_HOME Manualmente (si es necesario)

Si `JAVA_HOME` no está configurado:

1. Presiona `Win + Pause/Break` o busca "Variables de entorno"
2. Haz clic en **Variables de entorno**
3. En **Variables del sistema**, haz clic en **Nueva**
4. Nombre: `JAVA_HOME`
5. Valor: Ruta de instalación de Java (ej. `C:\Program Files\Eclipse Adoptium\jdk-17.0.9.9-hotspot`)
6. Haz clic en **Aceptar**
7. Edita la variable **Path**, agrega: `%JAVA_HOME%\bin`
8. Haz clic en **Aceptar** en todas las ventanas
9. Reinicia CMD para que los cambios surtan efecto

## 3.4 Instalación de GeoServer

### Paso 1: Descargar GeoServer

1. Ve a [https://geoserver.org/download/](https://geoserver.org/download/)
2. Descarga la versión **Stable** más reciente
3. Selecciona **Platform Independent Binary**
4. Descarga el archivo `.zip`

**Ejemplo**: `geoserver-2.24.1-bin.zip`

### Paso 2: Extraer GeoServer

1. Crea un directorio para GeoServer:
   ```
   C:\Program Files\GeoServer
   ```

2. Extrae el contenido del archivo `.zip` en ese directorio

3. La estructura debería verse así:
   ```
   C:\Program Files\GeoServer\
   ├── bin/
   ├── data_dir/
   ├── lib/
   ├── webapps/
   ├── logs/
   ├── start.jar
   └── ...
   ```

### Paso 3: Configurar Variables de Entorno (Opcional pero Recomendado)

Configura `GEOSERVER_HOME`:

1. Ve a **Variables de entorno**
2. Crea una nueva variable del sistema:
   - Nombre: `GEOSERVER_HOME`
   - Valor: `C:\Program Files\GeoServer`

Configura `GEOSERVER_DATA_DIR` (opcional):

1. Crea un directorio para datos:
   ```
   C:\GeoServerData
   ```

2. Crea variable:
   - Nombre: `GEOSERVER_DATA_DIR`
   - Valor: `C:\GeoServerData`

3. Copia el contenido de `C:\Program Files\GeoServer\data_dir` a `C:\GeoServerData`

### Paso 4: Iniciar GeoServer

#### Método 1: Script de Inicio (Recomendado para desarrollo)

1. Abre **CMD** como administrador
2. Navega al directorio bin:
   ```cmd
   cd "C:\Program Files\GeoServer\bin"
   ```

3. Ejecuta el script de inicio:
   ```cmd
   startup.bat
   ```

4. Espera a ver el mensaje:
   ```
   INFO: Starting ProtocolHandler ["http-bio-8080"]
   ```

#### Método 2: Instalar como Servicio de Windows

Para que GeoServer inicie automáticamente con Windows:

1. Descarga **NSSM** (Non-Sucking Service Manager):
   - [https://nssm.cc/download](https://nssm.cc/download)

2. Extrae `nssm.exe` en `C:\Program Files\GeoServer\bin`

3. Abre **CMD** como administrador:
   ```cmd
   cd "C:\Program Files\GeoServer\bin"
   nssm install GeoServer
   ```

4. En la ventana de NSSM:
   - **Path**: `C:\Program Files\GeoServer\bin\startup.bat`
   - **Startup directory**: `C:\Program Files\GeoServer\bin`
   - **Service name**: `GeoServer`

5. Haz clic en **Install service**

6. Inicia el servicio:
   ```cmd
   nssm start GeoServer
   ```

### Paso 5: Verificar la Instalación

1. Abre un navegador web
2. Ve a: [http://localhost:8080/geoserver](http://localhost:8080/geoserver)

Deberías ver la página de bienvenida de GeoServer.

### Solución de Problemas

#### Puerto 8080 Ocupado

Si el puerto 8080 ya está en uso:

1. Abre: `C:\Program Files\GeoServer\start.ini`
2. Busca la línea que contiene `jetty.http.port`
3. Cámbiala por:
   ```
   jetty.http.port=8090
   ```
4. Reinicia GeoServer
5. Accede en: [http://localhost:8090/geoserver](http://localhost:8090/geoserver)

#### Error de Java

Si aparece "Java not found":

1. Verifica que Java esté instalado: `java -version`
2. Verifica que `JAVA_HOME` esté configurado: `echo %JAVA_HOME%`
3. Reinicia la terminal después de configurar variables de entorno

#### GeoServer No Inicia

Revisa los logs:
```
C:\Program Files\GeoServer\logs\geoserver.log
```

## 3.5 Interfaz de Administración

### Acceso a la Interfaz

1. Abre: [http://localhost:8080/geoserver](http://localhost:8080/geoserver)
2. Haz clic en **Login** (esquina superior derecha)
3. Credenciales por defecto:
   - **Usuario**: `admin`
   - **Contraseña**: `geoserver`

**⚠️ IMPORTANTE**: Cambia la contraseña después del primer acceso.

### Cambiar la Contraseña de Administrador

1. Inicia sesión con las credenciales por defecto
2. Ve a **Security** → **Users, Groups, Roles**
3. Haz clic en la pestaña **Users/Groups**
4. Haz clic en el usuario `admin`
5. Cambia la contraseña
6. Haz clic en **Save**

### Secciones Principales de la Interfaz

#### 1. Panel Principal (Home)

- **About GeoServer**: Información de la versión
- **Server Status**: Estado del servidor
- **GeoServer Logs**: Registro de eventos
- **Contact Information**: Información de contacto

#### 2. Data

Administración de datos geoespaciales:

- **Layer Preview**: Vista previa de capas
- **Workspaces**: Espacios de trabajo (contenedores lógicos)
- **Stores**: Almacenes de datos (conexiones a fuentes)
- **Layers**: Capas publicadas
- **Layer Groups**: Grupos de capas
- **Styles**: Estilos SLD/CSS

#### 3. Services

Configuración de servicios OGC:

- **WMS**: Web Map Service
- **WFS**: Web Feature Service
- **WCS**: Web Coverage Service
- **WPS**: Web Processing Service

#### 4. Settings

Configuración global:

- **Global**: Configuración general del servidor
- **Image Processing**: Procesamiento de imágenes
- **Raster Access**: Acceso a datos ráster
- **Coverage Access**: Acceso a coberturas

#### 5. Tile Caching

Configuración de caché de teselas:

- **Tile Layers**: Capas cacheadas
- **Caching Defaults**: Configuración por defecto
- **Gridsets**: Conjuntos de grillas

#### 6. Security

Gestión de seguridad:

- **Settings**: Configuración de seguridad
- **Authentication**: Métodos de autenticación
- **Users, Groups, Roles**: Usuarios y permisos
- **Data**: Reglas de acceso a datos
- **Services**: Reglas de acceso a servicios

#### 7. Demos

Herramientas de demostración:

- **Demo requests**: Ejemplos de peticiones
- **SRS List**: Sistemas de referencia espacial
- **Reprojection Console**: Consola de reproyección
- **WCS request builder**: Constructor de peticiones WCS

### Configuración Inicial Recomendada

#### 1. Configuración Global

**Settings** → **Global**:

- **Verbose Messages**: - (durante desarrollo)
- **Verbose Exceptions**: - (durante desarrollo)
- **Number of Decimals**: `8`
- **Character Set**: `UTF-8`
- **Proxy Base URL**: `http://tu-servidor.com/geoserver` (en producción)

#### 2. Información de Contacto

**Settings** → **Contact Information**:

- **Contact Organization**: Tu organización
- **Contact Person**: Tu nombre
- **Contact Email**: tu@email.com

#### 3. Configuración de Servicios

**Services** → **WMS**:

- **Service Metadata**:
  - Title: "Servicio de Mapas"
  - Abstract: Descripción del servicio
- **Limited SRS List**: Agrega SRIDs que usarás (4326, 3857, 3116, etc.)

**Services** → **WFS**:

- **Service Metadata**: Similar a WMS
- **Service Level**: **Complete** (para operaciones WFS-T)
- **Maximum number of features**: `1000000`
- **Maximum number of decimals**: `8`

## 3.6 Vista Previa de Capas

GeoServer incluye capas de demostración.

### Acceder a Layer Preview

1. Ve a **Data** → **Layer Preview**
2. Verás una lista de capas precargadas
3. Para cada capa puedes:
   - **OpenLayers**: Vista previa interactiva
   - **GML, GeoJSON, CSV, etc.**: Descargar en diferentes formatos

### Ejemplo: Vista Previa de Estados (sf:states)

1. Busca la capa `sf:states`
2. Haz clic en **OpenLayers** en el menú desplegable
3. Se abrirá un mapa interactivo con la capa

### URLs de Servicios

Para cada capa, puedes obtener las URLs de servicio:

**WMS GetCapabilities**:
```
http://localhost:8080/geoserver/wms?service=WMS&version=1.1.0&request=GetCapabilities
```

**WFS GetCapabilities**:
```
http://localhost:8080/geoserver/wfs?service=WFS&version=1.1.0&request=GetCapabilities
```

**WMS GetMap** (ejemplo):
```
http://localhost:8080/geoserver/wms?
  service=WMS&
  version=1.1.0&
  request=GetMap&
  layers=sf:states&
  bbox=-124.73,24.96,-66.97,49.37&
  width=768&
  height=384&
  srs=EPSG:4326&
  format=image/png
```

## 3.7 Estructura de Directorios

### Directorio de Datos (data_dir)

```
data_dir/
├── coverages/          # Coberturas ráster
├── data/               # Datos por defecto
├── demo/               # Datos de demostración
├── layergroups/        # Configuración de grupos
├── logs/               # Logs de GeoServer
├── palettes/           # Paletas de colores
├── plugIns/            # Plugins instalados
├── security/           # Configuración de seguridad
├── styles/             # Archivos SLD
├── templates/          # Plantillas de salida
├── user_projections/   # Proyecciones personalizadas
├── workspaces/         # Configuración de workspaces
└── global.xml          # Configuración global
```

### Archivos Importantes

- `global.xml`: Configuración global
- `logging.xml`: Configuración de logs
- `wms.xml`: Configuración del servicio WMS
- `wfs.xml`: Configuración del servicio WFS

## 3.8 Monitoreo y Logs

### Ver el Estado del Servidor

**About & Status** → **Server Status**:

- **JVM Version**: Versión de Java
- **Memory Usage**: Uso de memoria
- **Update Sequence**: Secuencia de actualización
- **Resource Cache**: Estado de caché
- **Configuration**: Recargar configuración

### Limpiar Caché

Si realizas cambios en la configuración:

1. Ve a **Server Status**
2. Haz clic en **Reload** en la sección **Configuration**
3. O haz clic en **Clear** en **Resource Cache**

### Acceder a Logs

**About & Status** → **GeoServer Logs**:

- Muestra los últimos eventos del servidor
- Útil para depuración
- Filtra por nivel: INFO, WARN, ERROR

### Archivo de Log

Ubicado en:
```
C:\Program Files\GeoServer\logs\geoserver.log
```

## 3.9 Mejores Prácticas

### Desarrollo

- - Usa `startup.bat` para desarrollo
- - Activa mensajes detallados y excepciones
- - Revisa logs regularmente
- - Usa Layer Preview para probar cambios

### Producción

- - Instala como servicio de Windows
- - Cambia credenciales por defecto
- - Desactiva mensajes verbosos
- - Configura Proxy Base URL
- - Implementa control de acceso
- - Habilita HTTPS
- - Configura backup automático del data_dir
- - Monitorea uso de memoria y rendimiento

### Seguridad

- 🔒 Cambia usuario y contraseña de `admin`
- 🔒 Crea usuarios con permisos limitados
- 🔒 Restringe acceso a datos sensibles
- 🔒 Usa HTTPS en producción
- 🔒 Mantén GeoServer actualizado

## Resumen

En este capítulo has aprendido:

- Qué es GeoServer y sus capacidades
- Requisitos del sistema
- Instalar Java en Windows
- Instalar GeoServer en Windows
- Configurar GeoServer como servicio
- Navegar por la interfaz de administración
- Configuración inicial recomendada
- Visualizar capas de demostración
- Monitorear el servidor y revisar logs
- Mejores prácticas de seguridad

## Ejercicio Práctico

1. Instala Java y verifica la instalación
2. Descarga e instala GeoServer
3. Accede a la interfaz de administración
4. Cambia la contraseña del usuario `admin`
5. Explora las capas de demostración en Layer Preview
6. Visualiza la capa `sf:states` en OpenLayers
7. Accede a los logs del servidor
8. Verifica el estado del servidor

## Referencias

- GeoServer Documentation. (2024). *GeoServer User Manual*. https://docs.geoserver.org/stable/en/user/
- GeoServer. (2024). *GeoServer Download*. https://geoserver.org/download/
- Open Geospatial Consortium. (2024). *OGC Standards*. https://www.ogc.org/standards/
- Boundless. (2024). *Introduction to GeoServer*. http://workshops.boundlessgeo.com/geoserver-intro/
- Iacovella, S., & Youngblood, B. (2013). *GeoServer Beginner's Guide*. Packt Publishing.

---

**Capítulo anterior**: [Capítulo 2: Instalación de PostgreSQL y PostGIS](./capitulo-02-postgresql-postgis.md)

**Próximo capítulo**: [Capítulo 4: Servicios Web Geográficos](./capitulo-04-servicios-web-geograficos.md)
