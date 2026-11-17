# AMR 17 Motorsport · Generador de presupuestos

Aplicación web de una sola página para crear, guardar e imprimir presupuestos de un taller mecánico. No necesita backend: todo se guarda en el navegador (`localStorage`).

## Funcionalidades

- Datos del taller, cliente, vehículo y presupuesto.
- Líneas de repuestos y mano de obra con cantidad, coste, precio y margen.
- Cálculo automático de base imponible, IVA (editable), total y ganancia neta.
- Guardado con numeración correlativa `PRE-AAAA-0001`; guardar de nuevo el mismo número lo actualiza.
- Borrador autoguardado: al recargar la página no se pierde nada.
- Duplicar y crear presupuesto nuevo.
- Historial por matrícula: consultar, cargar o eliminar presupuestos anteriores.
- Importación de datos desde un PDF (PDF.js).
- Impresión del presupuesto completo (con costes y margen) o de la copia para el cliente (sin costes).
- Extensión de Chrome (`amr17_extension/`) que captura nombre, referencia y precio de una pieza en endado.com.

## Estructura

```
index.html            aplicación completa (HTML + CSS + JS)
amr17_extension/      extensión de Chrome (manifest v3)
tests/                pruebas con Playwright
versiones anteriores/ historial de versiones (solo consulta)
extension/            copia antigua de la app y de la extensión
```

## Ejecutar en local

```bash
npm install
npm start
```

Abrir `http://127.0.0.1:5500/index.html`.

Sin Node: `python -m http.server 5500 --bind 127.0.0.1`.

## Pruebas

Con el servidor arrancado:

```bash
npx playwright install chromium
npm test
```

## Extensión de Chrome

1. `chrome://extensions` → activar modo desarrollador.
2. "Cargar descomprimida" → carpeta `amr17_extension/`.
3. En una ficha de producto de endado.com aparece el panel "Añadir al presupuesto".
4. Abrir el popup de la extensión y pulsar "Cargar cola" para pasar las piezas a las líneas.

## Tecnologías

| Capa | Qué se usa |
|---|---|
| HTML | HTML5, formularios, tablas, `template`, atributos `aria-*` |
| CSS | Tailwind CSS (CDN), `@media print`, flexbox y grid |
| JavaScript | ES6+, Alpine.js 3, PDF.js 3.11, Web Storage, Blob, Intl |
| Extensión | Manifest V3, content script, `chrome.storage.local` |
| Herramientas | npm, http-server, Playwright, Git/GitHub |

## Qué contiene (HTML, CSS y JS)

**HTML**
- Estructura de documento: `doctype`, `lang`, `meta charset`, `meta viewport`, `head` y `body`.
- Formularios: `input` de tipo text, number, date, email y file; `textarea`; `button`; `label`.
- Atributos: `placeholder`, `readonly`, `min`, `step`, `accept`, `aria-label`.
- Tabla con `thead` y `tbody`; etiqueta `template` para repetir filas.
- Directivas de Alpine: `x-data`, `x-model`, `x-for`, `x-show`, `x-text`, `x-cloak`, `@click`, `:class`, `:disabled`.

**CSS**
- Tailwind en modo utilidades: espaciado, colores, bordes, sombras, tipografía.
- Diseño responsive con prefijo `md:`.
- Flexbox (`flex`, `justify-between`, `gap`) y grid (`grid-cols-2`).
- Estados `hover:` y `disabled:`.
- CSS propio: toasts con `position:fixed`, `[x-cloak]`, `@media print` con clases `no-print` y `print-only`.
- Plantilla de impresión independiente con su propio CSS (grid, `border-collapse`, `box-sizing`).

**JavaScript**
- Variables `const`/`let`, funciones flecha, template literals, spread y `?.`/`??`.
- Objetos, arrays y métodos `map`, `filter`, `reduce`, `find`, `findIndex`, `forEach`, `sort`.
- Getters (`get subtotal()`) para valores calculados.
- DOM: `getElementById`, `createElement`, `appendChild`, eventos.
- `async/await`, Promesas y `try/catch/finally`.
- `JSON.parse` / `JSON.stringify` y `localStorage`.
- Expresiones regulares para leer el PDF.
- `Blob`, `URL.createObjectURL`, `window.open`, `window.print`.
- `Intl.NumberFormat` y `Date` para moneda y fechas.
- Escape de HTML para evitar inyección en la copia de cliente.
- Extensión: `MutationObserver`, `chrome.storage.local`, content scripts.

## Qué debe saber un alumno de 2º DAW para defender este proyecto

**Cliente (DWEC / DIW)**
- Diferencia entre DOM real y reactividad de un framework (Alpine) frente a Angular, React o Vue.
- Ciclo de vida: evento → cambio de estado → actualización de la vista.
- Web Storage: `localStorage` vs `sessionStorage` vs cookies, límites (~5 MB) y que no es seguro ni compartido entre equipos.
- Asincronía: Promesas, `async/await`, `fetch` y carga de scripts externos.
- Diseño responsive, accesibilidad (`aria`, `label`, contraste), usabilidad y hoja de estilos de impresión.
- Seguridad en cliente: XSS, por qué se escapa el HTML y por qué no se debe confiar en datos del usuario.

**Servidor (DWES / Spring)**
- Qué le falta a la app para ser real: backend, API REST, base de datos, autenticación y copias de seguridad.
- Cómo se modelaría: entidades `Presupuesto`, `Cliente`, `Vehículo`, `Línea`; controladores, servicios y repositorios.
- Cómo se migraría el `localStorage` a una API con JSON y códigos HTTP.

**Despliegue**
- Servir contenido estático (Apache, Nginx, GitHub Pages, Netlify), HTTPS y dominio.
- Por qué el CDN de Tailwind no es para producción y cómo se compila con la CLI.
- Control de versiones con Git, `.gitignore`, ramas y releases; gestión de dependencias con npm.

**Calidad**
- Pruebas automáticas end-to-end con Playwright y qué se prueba en cada una.
- Depuración con las DevTools: consola, red, almacenamiento y pestaña Application.

## Limitaciones conocidas

- Los datos viven solo en el navegador: borrar la caché o cambiar de equipo los pierde.
- La importación de PDF está ajustada a un formato concreto (Autodata) y puede no leer otros.
- La app no carga sola la cola de la extensión: se hace desde el popup de la extensión.

## Licencia

ISC.
