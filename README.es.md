🌐 [English](README.md) | [Português](README.pt-BR.md) | Español

# request2code

Extensión de navegador para DevTools que captura solicitudes de red y las convierte en snippets de código listos para ejecutar. Disponible para Firefox y Chrome.

## Instalar

**Firefox** — Mozilla Add-ons Store:
[addons.mozilla.org/pt-BR/firefox/addon/request2code/](https://addons.mozilla.org/pt-BR/firefox/addon/request2code/)

**Chrome** — próximamente en la Chrome Web Store. Por ahora, cárgala manualmente (ver abajo).

## Funcionalidades

- Captura todas las solicitudes HTTP mientras DevTools está abierto
- Convierte cada solicitud en código ejecutable para 5 lenguajes:
  - **Python** — biblioteca `requests`
  - **JavaScript** — `fetch` con `async/await`
  - **cURL** — comando de línea
  - **PHP** — funciones `curl_*`
  - **Go** — paquete `net/http`
- Maneja correctamente todos los tipos de cuerpo: JSON, form-urlencoded, multipart/form-data, raw
- Separa las cookies del header `Cookie` en un parámetro dedicado (Python) o flag `-b` (cURL)
- Syntax highlighting para todos los lenguajes
- Visualización de la respuesta en tres modos: Formateado, Árbol interactivo (JSON), Raw
- Filtro por URL y por tipo de recurso (XHR/Fetch, JS, CSS, Img, Doc, Other)
- Resize drag entre los paneles
- Preserve log (mantiene las solicitudes al navegar a otra página)
- Interfaz en portugués, inglés y español

## Estructura del repositorio

```
request2code/
├── firefox/             # Extensión para Firefox (MV2, browser.* API)
│   ├── manifest.json
│   ├── devtools.html
│   ├── devtools.js
│   ├── panel.html
│   ├── panel.js
│   ├── lib/
│   │   └── converters.js
│   ├── _locales/
│   └── test/
├── chrome/              # Extensión para Chrome (MV3, chrome.* API)
│   ├── manifest.json
│   ├── devtools.html
│   ├── devtools.js
│   ├── panel.html
│   ├── panel.js
│   └── lib/
│       └── converters.js
└── docs/
    ├── architecture.md
    └── adding-converters.md
```

Cada carpeta es self-contained y puede empaquetarse independientemente para su envío a las tiendas.

## Desarrollo

Sin build step, sin bundler, sin dependencias de runtime. El código se carga directamente en el navegador.

### Cargar en Firefox (temporal)

```
about:debugging → This Firefox → Load Temporary Add-on → seleccionar firefox/manifest.json
```

### Cargar en Chrome (temporal)

```
chrome://extensions → activar "Modo desarrollador" → "Cargar descomprimida" → seleccionar la carpeta chrome/
```

### Recargar tras cambios

| Archivo modificado | Qué hacer |
|---|---|
| `panel.js` / `panel.html` | Cerrar y reabrir el panel DevTools |
| `manifest.json` / `devtools.js` | Firefox: recargar en `about:debugging` / Chrome: botón reload en `chrome://extensions` |

### Tests

Los tests cubren `lib/converters.js` — funciones puras sin dependencia de browser API.

```bash
npm test
```

Usa Jest. Los archivos de test están en `firefox/test/` e importan `firefox/lib/converters.js`.

### Sincronizar cambios entre versiones

`lib/converters.js` está duplicado en `firefox/lib/` y `chrome/lib/`. Cualquier cambio en los conversores o en el i18n debe aplicarse en ambos archivos.

La única diferencia entre las versiones está en:
- `manifest.json` — versión de manifest, browser-specific settings
- `devtools.js` — `browser.*` (Firefox) vs `chrome.*` (Chrome)
- `panel.js` — `browser.devtools.network.*` (Firefox) vs `chrome.devtools.network.*` (Chrome)

### Agregar un nuevo lenguaje

Ver [docs/adding-converters.md](docs/adding-converters.md).

### Publicar

**Firefox (AMO):**
```bash
cd firefox
npx web-ext lint
npx web-ext build
```

**Chrome (CWS):**
Comprime la carpeta `chrome/` como `.zip` y súbela desde el Chrome Developer Dashboard.
