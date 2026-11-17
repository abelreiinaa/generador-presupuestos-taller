# AMR 17 Motorsport · Generador de Presupuestos

Aplicación web SPA (*Single Page Application*) diseñada para crear, gestionar e imprimir presupuestos en un taller mecánico. Actualmente funciona de manera 100% autónoma en el navegador utilizando `localStorage` para la persistencia de datos, e incluye una extensión de Chrome complementaria para la captura automática de recambios.

## Funcionalidades actuales

- **Gestión integral:** Datos fiscales del taller, cliente, vehículo (matrícula, bastidor, km) y metadatos del presupuesto.
- **Cálculo financiero dinámico:** Líneas de repuestos y mano de obra con cálculo en tiempo real de base imponible, IVA editable, total y margen de beneficio neto.
- **Persistencia y autoguardado:** Numeración correlativa automática (`PRE-AAAA-0001`), actualización de presupuestos existentes, duplicado y autoguardado continuo en borrador para evitar pérdida de datos al recargar.
- **Historial por matrícula:** Búsqueda, consulta, carga y eliminación de presupuestos asociados a un vehículo.
- **Importación desde PDF:** Extracción y parseo automático de datos técnicos y tiempos desde documentos PDF (formato Autodata) mediante `PDF.js`.
- **Doble modo de impresión:** Generación de vista de impresión interna (con costes y márgenes) y copia limpia para el cliente (sin información interna).
- **Extensión de Chrome (`amr17_extension/`):** Integración con *endado.com* para capturar referencia, nombre y precio de piezas directamente a una cola de importación.

## Estructura del proyecto

```text
├── index.html               # Aplicación principal (estructura, estilos e interactividad)
├── amr17_extension/         # Extensión de Chrome (Manifest V3)
├── tests/                   # Suite de pruebas End-to-End con Playwright
├── versiones anteriores/    # Histórico de versiones previas
└── extension/               # Legado de versiones iniciales
```

## Instalación y ejecución local

### Con Node.js
```bash
npm install
npm start
```

### Sin dependencias (Python)
```bash
python -m http.server 5500 --bind 127.0.0.1
```

Abrir en el navegador: `http://127.0.0.1:5500/index.html`

## Pruebas automatizadas (E2E)

Con el servidor local levantado, ejecutar la batería de tests con Playwright:

```bash
npx playwright install chromium
npm test
```

## Instalación de la extensión de Chrome

1. Acceder a `chrome://extensions` y activar el **Modo de desarrollador**.
2. Pulsar **Cargar descomprimida** y seleccionar la carpeta `amr17_extension/`.
3. Navegar a cualquier ficha de producto en *endado.com* y utilizar el panel inyectado **"Añadir al presupuesto"**.
4. Abrir el popup de la extensión y pulsar **"Cargar cola"** para volcar las piezas en la aplicación.

## Stack Tecnológico

| Capa | Tecnologías y APIs |
| :--- | :--- |
| **Interfaz y Estilos** | HTML5 semántico, Tailwind CSS, Flexbox, CSS Grid, `@media print` |
| **Lógica y Reactividad** | JavaScript ES6+, Alpine.js 3, `Intl.NumberFormat`, Expresiones Regulares |
| **Procesamiento y Archivos** | `PDF.js 3.11`, `Blob API`, `URL.createObjectURL` |
| **Almacenamiento actual** | Web Storage API (`localStorage`) |
| **Extensión Web** | Chrome Manifest V3, Content Scripts, `MutationObserver`, `chrome.storage.local` |
| **Testing y Herramientas** | Playwright, Node.js / npm, `http-server`, Git |

## Arquitectura y Decisiones Técnicas

- **Reactividad ligera sin paso de compilación:** Se eligió **Alpine.js** para gestionar el estado reactivo (`x-data`, getters calculados para totales y márgenes) manteniendo la aplicación en un único archivo portable.
- **Aislamiento en generación de documentos:** Para la copia del cliente se genera un documento HTML independiente en memoria mediante `Blob` y `URL.createObjectURL`, aplicando sanitización de cadenas (escape de HTML) para prevenir vulnerabilidades XSS antes de invocar `window.print()`.
- **Extracción de datos en cliente:** El parseo de PDFs se realiza íntegramente en el navegador mediante `PDF.js` y expresiones regulares, garantizando la privacidad de los documentos sin subir archivos a terceros.
- **Observabilidad del DOM en la extensión:** El *content script* utiliza `MutationObserver` para detectar cambios dinámicos en el catálogo web e inyectar el botón de captura de forma resiliente.

## Roadmap: Próxima etapa (Implementación de Backend)

El proyecto se encuentra actualmente en su **Fase 1 (MVP Frontend)**. El siguiente paso crítico en el desarrollo es la **implementación de un Backend completo** para superar las limitaciones del almacenamiento local (`localStorage`) y permitir el trabajo multiusuario en red dentro del taller:

- [x] **Fase 1: Frontend y MVP Local-First** — Interfaz completa, cálculos en tiempo real, importación de PDF, impresión y extensión de Chrome.
- [ ] **Fase 2: Desarrollo de API REST (Backend)** — Creación del servidor con Spring Boot / Node.js estructurado en arquitectura por capas (Controladores, Servicios y Repositorios).
- [ ] **Fase 3: Base de Datos Relacional** — Migración de `localStorage` a PostgreSQL / MySQL modelando las entidades `Taller`, `Cliente`, `Vehiculo`, `Presupuesto` y `LineaPresupuesto`.
- [ ] **Fase 4: Seguridad y Control de Acceso** — Sistema de autenticación mediante tokens JWT y gestión de roles (administrador vs. mecánico).
- [ ] **Fase 5: Integración Full-Stack y Producción** — Conexión del frontend y de la extensión de Chrome directamente contra los endpoints del servidor, compilación de Tailwind CSS y despliegue en nube.

## Licencia

ISC