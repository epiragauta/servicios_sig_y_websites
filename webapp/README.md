# Aplicación Web - Visualización de Departamentos y Municipios de Colombia

Esta es una aplicación web interactiva que consume servicios WMS y WFS de GeoServer para visualizar la división administrativa de Colombia.

## Características

- 🗺️ Visualización de departamentos y municipios mediante WMS
- 🔍 Búsqueda de departamentos por nombre
- 📊 Información detallada al hacer clic
- 🎨 Múltiples capas base (OpenStreetMap, Satélite, CartoDB)
- 📱 Diseño responsive (funciona en móvil y desktop)
- ⚡ Interactividad con datos WFS (GeoJSON)
- 🎯 Highlight al pasar el mouse
- 📍 Control de escala y capas

## Requisitos Previos

1. **GeoServer** debe estar ejecutándose en `http://localhost:8080/geoserver`
2. Workspace `taller` creado en GeoServer
3. Capas `departamentos` y `municipios` publicadas
4. Datos cargados en PostgreSQL/PostGIS

## Estructura de Archivos

```
webapp/
├── index.html          # Página principal
├── css/
│   └── style.css      # Estilos personalizados
├── js/
│   └── app.js         # Lógica de la aplicación
└── README.md          # Este archivo
```

## Configuración

### 1. Verificar URLs de GeoServer

Abre `js/app.js` y verifica las URLs:

```javascript
const GEOSERVER_URL = 'http://localhost:8080/geoserver/taller/wms';
const WFS_URL = 'http://localhost:8080/geoserver/taller/wfs';
```

Si tu GeoServer está en otra dirección o puerto, modifica estas líneas.

### 2. Verificar Nombres de Capas

En `js/app.js`, verifica que los nombres de las capas coincidan con los publicados en GeoServer:

```javascript
const departamentosWMS = L.tileLayer.wms(GEOSERVER_URL, {
    layers: 'taller:departamentos',  // Formato: workspace:capa
    ...
});
```

## Instalación y Ejecución

### Método 1: Servidor HTTP Simple (Python)

Si tienes Python instalado:

```bash
# Python 3
cd webapp
python -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000
```

Luego abre en el navegador: [http://localhost:8000](http://localhost:8000)

### Método 2: Node.js http-server

Si tienes Node.js:

```bash
# Instalar http-server globalmente
npm install -g http-server

# Ejecutar
cd webapp
http-server -p 8000
```

Abre: [http://localhost:8000](http://localhost:8000)

### Método 3: Abrir Directamente

Puedes abrir `index.html` directamente en el navegador, pero es posible que encuentres problemas de CORS al cargar datos desde GeoServer.

**Recomendado**: Usar un servidor HTTP local.

## Solución de Problemas

### Error: No se pueden cargar los datos de departamentos

**Causa**: GeoServer no está ejecutándose o las URLs son incorrectas.

**Solución**:
1. Verifica que GeoServer esté corriendo: [http://localhost:8080/geoserver](http://localhost:8080/geoserver)
2. Verifica que el workspace `taller` existe
3. Verifica que las capas `departamentos` y `municipios` están publicadas
4. Revisa la consola del navegador (F12) para ver errores específicos

### Error de CORS

**Causa**: El navegador bloquea peticiones cross-origin.

**Solución**:
1. Usa un servidor HTTP local (Python o Node.js)
2. O configura CORS en GeoServer (ver documentación del Capítulo 8)

### Las capas WMS no se muestran

**Causa**: Nombres de capas incorrectos o GeoServer no responde.

**Solución**:
1. Verifica los nombres en GeoServer: **Data** → **Layers**
2. Prueba la URL WMS directamente en el navegador:
   ```
   http://localhost:8080/geoserver/taller/wms?service=WMS&request=GetCapabilities
   ```
3. Verifica que las capas estén habilitadas (Enabled = ✅)

### La búsqueda no funciona

**Causa**: Los datos WFS no se cargaron correctamente.

**Solución**:
1. Abre la consola del navegador (F12)
2. Busca mensajes de error
3. Verifica que la URL WFS sea correcta
4. Prueba la URL WFS en el navegador:
   ```
   http://localhost:8080/geoserver/taller/wfs?service=WFS&version=2.0.0&request=GetFeature&typeName=taller:departamentos&outputFormat=application/json&count=1
   ```

## Personalización

### Cambiar Colores

Edita `css/style.css`:

```css
.header {
    background: linear-gradient(135deg, #TU_COLOR1 0%, #TU_COLOR2 100%);
}
```

### Cambiar Centro del Mapa

Edita `js/app.js`:

```javascript
const MAP_CONFIG = {
    center: [latitud, longitud],  // Ej: [4.5709, -74.2973]
    zoom: 6
};
```

### Agregar Más Capas Base

Edita `js/app.js` en la sección `baseLayers`:

```javascript
const baseLayers = {
    'Tu Capa': L.tileLayer('URL_DE_TU_CAPA', {
        attribution: 'Atribución',
        maxZoom: 19
    })
};
```

### Modificar Estilos de Features

Edita la función `featureStyle` en `js/app.js`:

```javascript
function featureStyle(feature) {
    return {
        fillColor: '#TU_COLOR',    // Color de relleno
        weight: 2,                  // Grosor del borde
        opacity: 0.8,              // Opacidad del borde
        color: '#BORDE_COLOR',     // Color del borde
        fillOpacity: 0.4           // Opacidad del relleno
    };
}
```

## Despliegue en Producción

### GitHub Pages

1. Sube el código a GitHub
2. Ve a **Settings** → **Pages**
3. Selecciona la rama y carpeta `/webapp`
4. Guarda

**Nota**: Actualiza las URLs de GeoServer a tu servidor público.

### Netlify

1. Arrastra la carpeta `webapp` a [Netlify Drop](https://app.netlify.com/drop)
2. Tu sitio estará en línea en segundos

### Servidor Propio

1. Copia los archivos a tu servidor web (Apache, Nginx, etc.)
2. Configura HTTPS
3. Actualiza las URLs de GeoServer

## Tecnologías Utilizadas

- **Leaflet.js** 1.9.4 - Biblioteca de mapas interactivos
- **GeoServer** - Servidor de mapas OGC
- **HTML5/CSS3** - Estructura y estilos
- **JavaScript ES6** - Lógica de la aplicación
- **OGC WMS/WFS** - Estándares de servicios web geográficos

## Recursos Adicionales

- [Documentación de Leaflet](https://leafletjs.com/)
- [Tutoriales de Leaflet](https://leafletjs.com/examples.html)
- [Documentación de GeoServer](https://docs.geoserver.org/)
- [Estándares OGC](https://www.ogc.org/standards/)

## Licencia

Este proyecto es parte del Taller de Servicios Web Geográficos y está disponible para fines educativos.

## Autor

Taller de Servicios Web Geográficos - 2024

---

**¿Problemas?** Consulta los capítulos del taller o abre la consola del navegador (F12) para ver mensajes de error detallados.
