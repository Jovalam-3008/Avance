# Bonsai Corp - Optimizacion del Portafolio de Cajas

README provisional del proyecto.

## Objetivo

Ayudar a Bonsai Corp a optimizar su portafolio de cajas para reducir complejidad operativa, mejorar la utilizacion de pallets y estimar oportunidades de ahorro en packaging y logistica.

El proyecto parte de cuatro datasets principales y busca construir una propuesta de consolidacion de cajas que sea tecnicamente factible, economicamente defendible y facil de comunicar.

## Estructura actual

```text
.
+-- README.md
+-- Descripcion.txt
+-- plan_proyecto_bonsai.md
+-- data/
    +-- catalogo_productos.csv
    +-- especificaciones_cajas.csv
    +-- operaciones_planta.csv
    +-- procurement_cajas.csv
```

## Datasets

### `data/catalogo_productos.csv`

Catalogo central de SKUs. Incluye identificador de producto, descripcion comercial, atributos de producto, peso neto, cantidad de paquetes y el tipo de caja asignado.

Campo clave:

```text
codigo_producto
caja_tipo_id
```

### `data/especificaciones_cajas.csv`

Tabla tecnica de cajas. Incluye dimensiones interiores y exteriores, grosor, configuracion de apilado en pallet, cantidad total de cajas por pallet y utilizacion.

Campo clave:

```text
caja_tipo_id
```

### `data/operaciones_planta.csv`

Tabla operativa por SKU. Incluye volumen por canal, volumen por planta, cantidad de pallets y costos logisticos.

Campo clave:

```text
codigo_producto
```

### `data/procurement_cajas.csv`

Tabla de compras por tipo de caja y planta. Incluye volumen por caja/planta, costo unitario base, costos unitarios por planta, descuentos y costo por pallet.

Campo clave:

```text
caja_tipo_id
```

## Estado inicial del analisis

Hallazgos preliminares:

- Hay 435 filas de productos/operaciones y 421 `codigo_producto` unicos.
- Existen 14 codigos de producto duplicados; algunos duplicados tienen diferencias reales en caja, pack size o volumen.
- Hay 204 tipos de caja.
- Los cuatro datasets pueden integrarse mediante `codigo_producto` y `caja_tipo_id`.
- `procurement_cajas.csv` requiere limpieza antes de usarse para costos, porque los volumenes por tipo/planta parecen estar multiplicados por la cantidad de SKUs asociados a cada caja.
- Hay valores `ERROR` en costos unitarios por planta, imputables desde costo base y descuento.
- Algunas cajas tienen `cantidad_cajas_total` vacio, pero el valor puede derivarse desde alto, largo y ancho.

## Plan de trabajo

El plan detallado esta documentado en:

```text
plan_proyecto_bonsai.md
```

Fases principales:

1. Comprension del problema y criterio de evaluacion.
2. Preparacion de datos e integracion de tablas.
3. Analisis exploratorio riguroso.
4. Investigacion bibliografica y revision de proyectos similares.
5. Ingenieria de caracteristicas.
6. Modelado y/o optimizacion.
7. Evaluacion de escenarios.
8. Optimizacion de parametros.
9. Modelado final.
10. Analisis de resultados.
11. Presentacion de resultados.
12. Cierre y defensa de la propuesta.

## Enfoque metodologico

Este proyecto se tratara principalmente como un problema de optimizacion operativa, no como un problema clasico de prediccion supervisada.

La evaluacion priorizara KPIs de negocio:

- Ahorro estimado en packaging.
- Ahorro o impacto en pallets/logistica.
- Reduccion de cantidad de tipos de caja.
- Mejora de utilizacion ponderada de pallet.
- Numero de SKUs reasignados.
- Factibilidad fisica de cada reasignacion.
- Riesgo de implementacion.

## Proximos pasos

1. Construir un dataset integrado limpio.
2. Crear una clave tecnica para manejar duplicados de producto.
3. Corregir volumenes de procurement y costos unitarios.
4. Validar formulas de pallets por SKU-planta.
5. Ejecutar EDA completo.
6. Construir la linea base de costos.
7. Definir reglas de compatibilidad entre cajas.
8. Generar escenarios de consolidacion.

## Notas

Este README es provisional y debe actualizarse conforme avance el proyecto, especialmente cuando se creen notebooks, scripts, outputs y reportes finales.
