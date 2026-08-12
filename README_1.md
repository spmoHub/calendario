# Calendario de operación · Encuestas institucionales

Sitio estático que publica las fechas clave de operación de los instrumentos de
satisfacción y experiencia de Universidad Tecmilenio. Se alimenta de un solo
archivo de Excel y se actualiza una vez al año.

---

## Estructura del repositorio

```
calendario-encuestas/
├── index.html              ← el sitio completo (un solo archivo)
├── README.md
└── data/
    └── calendario.xlsx     ← FUENTE ÚNICA DE VERDAD (lo único que se edita)
```

El sitio lee `data/calendario.xlsx` en el navegador (con SheetJS) siempre que se
sirva por HTTP. Si no lo encuentra, usa una copia de respaldo incrustada en el
propio `index.html`, para que nunca se quede en blanco.

---

## Cómo publicarlo en GitHub Pages

1. Crea el repositorio (público o privado con Pages habilitado) y sube estos archivos
   respetando la estructura de carpetas.
2. En **Settings → Pages**, elige *Deploy from a branch*, rama `main`, carpeta `/ (root)`.
3. En 1–2 minutos queda en `https://<organizacion>.github.io/<repositorio>/`.

No hay build, ni dependencias, ni servidor. Es HTML + CSS + JS puro.

> Si prefieres SharePoint o un servidor interno en lugar de GitHub Pages, funciona
> igual: solo tiene que servirse por HTTP (abrirlo con doble clic desde el disco
> bloquea la lectura del Excel por seguridad del navegador y caería a los datos
> incrustados).

---

## Cómo se actualiza cada año

1. Abre `data/calendario.xlsx`, hoja **Datos**.
2. Copia los renglones del año que cierra, pégalos abajo, cambia la columna `anio`
   y ajusta las fechas. **No borres los renglones viejos**: ahí vive el histórico.
3. Guarda, sube el archivo al repositorio (arrastrar y soltar desde la web de GitHub
   basta) y recarga el sitio.

Reglas de captura, diccionario de columnas y listas desplegables están en la hoja
**Instrucciones** del propio Excel.

---

## Esquema de datos

Un renglón = una aplicación de un instrumento en un periodo y nivel.

| Columna | Qué guarda |
|---|---|
| `id` | Identificador único, no se repite entre años |
| `anio` | Año calendario de operación. Es la llave del histórico |
| `periodo` | EM26, EA26, MA26, AD26, SD26, Trim 1, Bim 3, EM26 B1 P1… |
| `periodicidad` | Semestral · Tetramestral · Trimestral · Bimestral · Bloques |
| `instrumento` | Opina · ECAG · MAPS · LAN · Pulso Docente · Ev Docente · Empleabilidad |
| `categoria` | Compromisos Institucionales · Monitoreo |
| `publico` | Público de interés que responde: Alumnos · Docentes · Egresados |
| `nivel_plan` | Preparatoria · Profesional · Profesional Adultos · Ejecutivo y Maestría · Todos los niveles |
| `recepcion_bases` | Fecha límite para recibir la base de contactos (AAAA-MM-DD) |
| `aplicacion_inicio` | Primer día en campo |
| `aplicacion_fin` | Último día en campo |
| `entrega_resultados` | Fecha comprometida de entrega de resultados o indicadores |
| `area_responsable` | Área dueña del entregable (opcional) |
| `notas` | Supuestos, aclaraciones y pendientes por confirmar |

Reglas de coherencia que conviene respetar:

- `recepcion_bases` ≤ `aplicacion_inicio`
- `aplicacion_inicio` ≤ `aplicacion_fin`
- `entrega_resultados` ≥ `aplicacion_fin`
- Si el instrumento solo tiene entregable (Empleabilidad), las fechas de aplicación van vacías.

`build_data.py` incluye esas validaciones y las imprime al ejecutarse.

---

## Vistas del sitio

- **Cinta anual** — los 12 meses en una sola pantalla, una línea por aplicación.
  Barra = ventana de aplicación (color según categoría), rombo = recepción de bases,
  triángulo = entrega de resultados, línea verde = hoy.
- **Agenda** — hitos ordenados por fecha, de hoy en adelante, agrupados por mes.
- **Tabla** — el detalle completo, con descarga del subconjunto filtrado en CSV.

Los filtros (público, instrumento, categoría, nivel, búsqueda libre) aplican a las
tres vistas al mismo tiempo.

---

## Nota sobre los datos de 2026

La carga inicial viene del archivo
`Calendario_Inteligencia_y_Efectividad_Institucional_2026.xlsx`. Al normalizarlo
quedaron marcadas en la columna `notas` varias fechas que no cuadran con el
calendario 2026 y que conviene confirmar antes de publicar el sitio.
