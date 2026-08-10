# España · 7 joyas

Landing interactiva sobre las siete joyas de España: Sevilla, Madrid, Barcelona, Valencia, San Sebastián, Formentera y Lanzarote.

**Demo en vivo:** https://pukssito.github.io/ciudades-espana/

## Qué hace

- **Carousel hero** con Ken Burns, autoplay pausable y barra de progreso por dot.
- **Panel de ciudad** que se abre con un *morph* de la imagen del slide al header del panel usando la **View Transitions API** (`document.startViewTransition`), con fallback a transición CSS clásica.
- **Tiempo en vivo** de las 7 ciudades vía **Open-Meteo** (sin API key), con iconos WMO propios.
- **Mapa + rutas**: Google Maps embed sin API key y enlace de indicaciones desde la ubicación del usuario.
- **Galería de fotos** por ciudad.
- Accesibilidad: `prefers-reduced-motion`, `aria` en carousel/panel/galería, focus visible, navegación por teclado.

## Arquitectura

Un único `index.html` (sin build, sin dependencias):

- **CSS**: variables de diseño (`--tint-a/b` por ciudad para los resplandores), estilos editoriales, animaciones `@keyframes` (kenburns, dot-progress, panel-rise), y pseudo-elementos de View Transitions.
- **JS** (IIFE, `"use strict"`):
  - Carousel con timer basado en **deadline + `pause()`/`resume()`** en lugar de `setInterval`: al abrir el panel o la galería se congela el tiempo restante (y la barra del dot vía `animation-play-state`), y al cerrar se retoma desde el mismo punto.
  - `openCity(name, originImgEl)` / `closePanel()`: swap dinámico de `view-transition-name` entre la imagen del slide y la del panel para el morph de ida y vuelta (los nombres duplicados invalidarían la transición: se asigna al slide antes, se quita y se asigna al panel dentro del callback).
  - Datos de ciudades en `CITIES`, galería en `GALLERY`, tiempo en `WEATHER` (lat/lon).

## Cómo ejecutar

Abrir `index.html` en un navegador moderno (Chrome/Edge 111+, Safari 18+, Firefox 131+ para el morph). Para desarrollo con servidor:

```bash
python -m http.server 8787
```

## Gotchas documentados

- `file://` bloquea algunas APIs y Google Maps embed; usar HTTP.
- El panel reutiliza el `src` exacto de la imagen del slide para que el morph sea seamless (el `img` de `CITIES` usa otro width).
- El autoplay NO se pausa por hover (un `mouseenter` fantasma al cargar dejaba el timer congelado); solo se pausa con overlays abiertos o foco de teclado.
