# Fase 1 - Comprension del Problema y Criterio de Evaluacion

## 1. Definicion del problema

Bonsai Corp distribuye brocoli congelado desde cinco plantas y mantiene un catalogo amplio de tipos de caja. Esta variedad incrementa la complejidad de compras, inventario, operacion de planta, palletizacion y costos logisticos.

El problema no consiste solamente en reducir la cantidad de cajas. La decision debe equilibrar ahorro economico, factibilidad fisica, eficiencia de pallet y facilidad de implementacion.

Objetivo de negocio:

- Consolidar el portafolio de cajas.
- Reducir costos de packaging y pallets.
- Mejorar la utilizacion del pallet.
- Crear un protocolo para evitar proliferacion futura de cajas.

## 2. Pregunta principal

Que combinacion de tipos de caja deberia mantener Bonsai Corp para atender su catalogo actual de SKUs con menor costo total, menor complejidad y sin romper restricciones fisicas u operativas?

## 3. Preguntas analiticas

- ¿Cuales son las cajas con mayor volumen y mayor impacto economico?
- ¿Cuales son las cajas de bajo volumen o uso marginal?
- ¿Que cajas tienen baja utilizacion de pallet?
- ¿Que SKUs podrian migrar a una caja existente?
- ¿Que cajas conviene mantener aunque no sean las mas baratas?
- ¿Que ahorros se generan por packaging, pallets y descuentos por volumen?
- ¿Que plantas concentran mayor oportunidad de ahorro?
- ¿Que cambios son faciles de implementar y cuales requieren validacion adicional?

## 4. Alcance del proyecto

Incluido:

- Analisis de SKUs existentes.
- Analisis de tipos de caja existentes.
- Integracion de datos de catalogo, especificaciones, operaciones y procurement.
- Evaluacion de costos de packaging y pallets.
- Reasignacion de SKUs a cajas existentes, cuando sea factible.
- Definicion de reglas para nuevos lanzamientos.

Fuera de alcance inicial:

- Rediseño completo de nuevas cajas.
- Cambios en peso neto por caja.
- Cambios en cantidad de paquetes o presentacion comercial.
- Forecast de demanda futura.
- Optimizacion de rutas de transporte.
- Validacion fisica real en planta.

## 5. Unidad de analisis

La unidad minima recomendada para analisis es `sku_line_id` por planta.

Razon:

- `codigo_producto` aparece duplicado en algunos casos.
- Algunos duplicados tienen diferencias reales en caja, pack size o volumen.
- El costo de pallets debe calcularse por planta, no solo a nivel total.

Clave tecnica propuesta:

```text
sku_line_id = codigo_producto + numero_de_ocurrencia_en_catalogo
```

Tablas base:

- `data/catalogo_productos.csv`
- `data/especificaciones_cajas.csv`
- `data/operaciones_planta.csv`
- `data/procurement_cajas.csv`

## 6. Supuestos iniciales

- Las dimensiones interiores de la caja actual son una aproximacion del requerimiento fisico minimo del SKU.
- Una caja candidata es factible si sus dimensiones interiores son suficientes para contener el producto o la caja actual de referencia.
- La propuesta debe priorizar cajas existentes antes de sugerir redisenio.
- Los volumenes anuales representan una base suficiente para estimar impacto economico.
- Los descuentos por volumen pueden recalcularse o imputarse desde las reglas presentes en procurement.
- Los valores `ERROR` en costos unitarios son errores de formato/calculo y pueden corregirse desde `costo_unitario_base` y descuento.
- Los valores faltantes en `cantidad_cajas_total` pueden derivarse desde alto, largo y ancho.

## 7. Restricciones de factibilidad

Restricciones duras:

- La caja propuesta debe ser fisicamente compatible.
- No se modifica el peso neto por caja.
- No se modifica la cantidad de paquetes.
- El calculo de pallets debe mantenerse por SKU-planta.
- No se recomienda reasignacion si depende de datos no corregidos.

Restricciones blandas:

- Evitar reasignar SKUs de alto volumen si el ahorro es marginal.
- Evitar cambios que afecten muchas plantas sin beneficio claro.
- Preferir cajas con mejor utilizacion de pallet.
- Preferir cajas que ya tengan volumen relevante.
- Preferir soluciones explicables para negocio y operaciones.

## 8. Criterio de decision

Una propuesta sera considerada mejor que el baseline si cumple estas condiciones:

- Mantiene factibilidad fisica.
- Reduce costo total esperado.
- Reduce la cantidad de tipos de caja activos.
- Mejora o no deteriora significativamente la utilizacion de pallet.
- Mantiene un nivel de reasignaciones razonable.

Regla de desempate:

- Si dos propuestas tienen ahorro parecido, se elige la que requiera menos cambios.
- Si dos propuestas requieren cambios parecidos, se elige la que afecte menos plantas.
- Si dos propuestas tienen impacto operativo parecido, se elige la que tenga mayor utilizacion de pallet.

## 9. KPIs primarios

| KPI | Definicion | Nivel | Uso |
| --- | --- | --- | --- |
| Ahorro total estimado | Costo baseline - costo propuesto | Escenario | Medir beneficio principal |
| Ahorro packaging | Costo packaging baseline - costo packaging propuesto | Escenario / caja / planta | Medir impacto de procurement |
| Ahorro pallets | Costo pallets baseline - costo pallets propuesto | Escenario / caja / planta | Medir impacto logistico |
| Tipos de caja activos | Conteo de `caja_tipo_id` con volumen asignado | Escenario | Medir simplificacion |
| Reduccion de cajas | Cajas activas baseline - cajas activas propuestas | Escenario | Medir consolidacion |
| SKUs reasignados | Conteo de `sku_line_id` con cambio de caja | Escenario | Medir esfuerzo de implementacion |
| Utilizacion ponderada de pallet | Promedio de `utilizacion` ponderado por volumen | Escenario / caja | Medir eficiencia operativa |
| Volumen en baja utilizacion | Volumen en cajas con utilizacion menor a 80%, 85% o 90% | Escenario | Medir oportunidad operativa |

## 10. KPIs secundarios

| KPI | Definicion | Uso |
| --- | --- | --- |
| Cajas single-SKU | Cajas usadas por una sola linea SKU | Detectar complejidad innecesaria |
| Volumen por caja | Suma de volumen asignado a cada caja | Priorizar impacto |
| Costo por unidad | Costo total dividido por volumen | Comparar eficiencia |
| Costo por kg | Costo total dividido por kg estimados | Comparar presentaciones |
| Plantas afectadas | Conteo de plantas con cambios propuestos | Medir riesgo operativo |
| Familias afectadas | Conteo de categorias/subcategorias con cambios | Medir riesgo comercial |
| Concentracion top cajas | Share de volumen en top 10, 20, 50 cajas | Medir estructura del portafolio |

## 11. Baseline requerido

Antes de optimizar, se debe construir un baseline validado con:

- Cantidad actual de cajas activas.
- Volumen total anual.
- Volumen por planta.
- Pallets actuales.
- Costo actual de pallets.
- Costo actual de packaging.
- Utilizacion ponderada actual.
- Distribucion de SKUs por caja.

El baseline debe corregir los problemas conocidos de datos antes de usarse como referencia.

## 12. Criterios de aceptacion de la fase

La fase 1 se considera cerrada cuando existan:

- Definicion clara del problema.
- Alcance y fuera de alcance.
- Supuestos iniciales documentados.
- Restricciones duras y blandas.
- KPIs primarios y secundarios.
- Regla de decision para comparar escenarios.
- Lista de preguntas que debera responder el EDA.

## 13. Riesgos iniciales

- Las dimensiones de cajas pueden no capturar completamente restricciones reales de producto.
- Los descuentos por volumen pueden depender de reglas comerciales no visibles en los datos.
- Los duplicados de `codigo_producto` pueden representar errores o variantes reales.
- El ahorro teorico puede no coincidir con ahorro implementable si hay contratos o restricciones de proveedor.
- La consolidacion puede requerir pruebas fisicas antes de implementarse.

## 14. Resultado esperado de esta fase

El resultado de la fase 1 es una base metodologica para evaluar cualquier propuesta de consolidacion. A partir de aqui, la fase 2 debe preparar los datos para que estos KPIs puedan calcularse de forma reproducible.

