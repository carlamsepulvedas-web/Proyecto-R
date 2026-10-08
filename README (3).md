# Proyecto Finanzas en R — Priorización BIA/PCN

Priorización de Puntos Operativos según Impacto Financiero (BIA)
Alineado a ISO 22301 (Gestión de Continuidad del Negocio)




## 1\. Descripción de la solución

### ¿Qué soluciona?

Hoy, cuando ocurre un evento que interrumpe la operación de una
estación de servicio (corte eléctrico, falla de sistemas, evento
social, catástrofe), la priorización de a qué punto operativo atender
primero se basa en el **Tier operativo** asignado (1 a 4), un criterio
principalmente logístico. Ese criterio no incorpora de forma explícita
**cuánto dinero se deja de percibir por cada hora que el punto está
caído**.

Este proyecto construye un **índice de criticidad financiera** por
estación, que combina:

* el ingreso estimado que se pierde por hora de interrupción,
* el tiempo máximo tolerable de caída (MTPD) según su Tier,
* un puntaje BIA (Business Impact Analysis) asociado a ese Tier y a su
nivel de resiliencia (respaldo eléctrico, conectividad de respaldo).

Con ese índice, agrupa las 336 estaciones en 3 niveles de prioridad
(**Crítico / Importante / Tolerable**) mediante clustering (K-means),
para que la toma de decisiones ante un evento — o la inversión
preventiva en respaldo — se pueda ordenar por impacto financiero real
y no solo por criterio logístico.

### ¿A quiénes impacta?

* **Riesgo y Control Interno**: insumo directo para los planes de
continuidad del negocio (PCN) y la matriz BCP.
* **Gerencia de Operaciones**: priorización de restablecimiento
durante una contingencia real.
* **Planificación de inversión**: identificar qué estaciones críticas
carecen de respaldo eléctrico o conectividad, y priorizar ahí la
inversión.

### ¿Cómo se resuelve?

Un pipeline en R, reproducible, que toma el archivo de puntos
operativos, calcula el índice de criticidad de cada estación, las
agrupa en 3 niveles con K-means, y entrega una tabla priorizada más
una visualización.

### Periodicidad

Ejecutar el nálisis **trimestralmente**, o cada vez
que cambie la venta promedio, el Tier, o el equipamiento de respaldo
de alguna estación.

### Alcance

**L**as 336 estaciones de `Data\_ptos\_operativos\_PCN.xlsx`,
variables de ventas, Tier, respaldo eléctrico (GE) y conectividad de
respaldo (Starlink), e impacto financiero **por hora** de interrupción.

**Qué NO considera:** la matriz BCP de oficinas administrativas
(`Matriz\_BCP\_26.xlsx`, un activo distinto), la **probabilidad** de
ocurrencia de cada amenaza.

## 2\. Planificación del trabajo

|Fase|Entregable|Contenido|
|-|-|-|
|1|Entrega 01|Descripción del problema + planificación|
|2|Entrega 02|MVP: script R que calcula el índice y lo visualiza|
|3|Entrega 03|Documentación, despliegue y monitoreo|

La carta Gantt está hecha en R con `ggplot2` (`Entrega 01/gantt\_planificacion.R`),
con las fechas de entrega: 15/09, 30/09 y 07/10.

## 3\. MVP (código)

El script `Entrega 02/mvp\_bia\_pcn\_336.R` utiliza la planilla de data de 365
estaciones y calcula, para cada una, un índice de criticidad
financiera, agrupándolas en 3 niveles con K-means.

### Supuestos usado

|Supuesto|Regla aplicada|
|-|-|
|RTO y MTPD por Tier|Tier 1 → 4h/8h · Tier 2 → 8h/24h · Tier 3 → 12h/48h · Tier 4 → 24h/72h|
|Venta diaria en litros|`PROM V` (m³/mes) × 1.000 ÷ 30|
|Precio por litro|Constante: $1.150 CLP/L|
|Puntaje BIA (0-100)|Base por Tier (85/65/45/25) − 10 si no tiene generador − 5 si no tiene Starlink|

### 

### Resultado obtenido

|Nivel|N° estaciones|Exposición financiera por hora (CLP)|
|-|-|-|
|Crítico|80|$65.311.327|
|Importante|183|$83.582.648|
|Tolerable|73|$9.585.199|
|**Total**|**336**|**$158.479.174 / hora**|

Dato relevante: **155 estaciones (46%) no tienen generador de
respaldo** y **264 (79%) no tienen conectividad Starlink**, cruzando
esto con el nivel "Crítico", se puede priorizar inversión en
resiliencia donde más impacto financiero protege.

### Cómo correrlo en Colab

1. Cambiar el runtime a R (Entorno de ejecución → Cambiar tipo de
entorno de ejecución 
2. Subir `Data\_ptos\_operativos\_PCN.xlsx`.
3. Pega el script por celdas
4. Descargar `resultados\_priorizacion\_336.csv` y el gráfico generado.

### renv

Al final de la sesión en Colab:

```r
install.packages("renv")
renv::init()
renv::snapshot()
```

Descarga el `renv.lock` resultante y súbelo a `Entrega 02/`.

\---

## 4\. Documentación

**Modelo:** Índice de Criticidad Financiera BIA/PCN — scoring +
clustering no supervisado (K-means).

**Uso:** apoyar decisiones de priorización de continuidad del
negocio.

**Limpieza de datos:** números, texto "150 kVA", "NO", vacíos, y se modificó

a TRUE/FALSE; `starlink` se
interpretó como presencia de un código de cuenta = tiene respaldo.

**Limitaciones:** precio constante simplifica la realidad; el
modelo mide impacto, no probabilidad, debe combinarse con la matriz
BCP para obtener riesgo real.

## 5\. Despliegue

* **Corto plazo:** script en Colab, ejecución manual.
* **Mediano plazo:** mover a un entorno R persistente, leyendo los
datos directo desde la fuente de la compañía.
* **Integración:** conectar el CSV de resultados a Power BI para que
PCN consulte la priorización sin correr código.
* **Extensión futura:** utilizer la base de con `Matriz\_BCP\_26.xlsx` para combinar
impacto financiero con amenazas y estrategias ya definidas.

## 6\. Monitoreo

|Qué monitorear|Frecuencia|
|-|-|
|Frescura de los datos de venta|Trimestral|
|Estabilidad de los clusters mes a mes|Cada corrida|
|Cobertura de datos de `GE` y `starlink`|Trimestral|
|Validez de los supuestos (precio, mapeo Tier)|Semestral|
|Errores de ejecución del script|Cada corrida|

\---

## Estructura del repositorio

```
Entrega 01/   → descripción, planificación, carta Gantt
Entrega 02/   → MVP (código), resultados, renv.lock
Entrega 03/   → documentación detallada, despliegue, monitoreo
```

