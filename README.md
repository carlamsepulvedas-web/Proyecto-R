# Entrega 01 — Descripción de la solución y Planificación

## Priorización de Puntos Operativos según Impacto Financiero (BIA)

**Proyecto del curso — Finanzas en R**
Alineado a ISO 22301 (Gestión de Continuidad del Negocio)


## 1\. Descripción de la solución

### ¿Qué soluciona?

Hoy, cuando ocurre un evento que interrumpe la operación de una estación de
servicio (corte eléctrico, falla de sistemas, evento social, catástrofe),
la priorización de a qué punto operativo atender primero se basa en el
**Tier operativo** asignado (1 a 4), un criterio principalmente logístico.
Ese criterio no incorpora de forma explícita **cuánto dinero se deja de
percibir por cada hora que el punto está caído**.

Esta solución construye un **índice de criticidad financiera** por estación,
que combina:

* el ingreso estimado que se pierde por hora de interrupción,
* el tiempo máximo tolerable de caída (MTPD) según su Tier,
* el puntaje BIA (Business Impact Analysis) asociado a ese Tier y a su
nivel de resiliencia (respaldo eléctrico, conectividad de respaldo).

Con ese índice, agrupa las 336 estaciones en 3 niveles de prioridad
(**Crítico / Importante / Tolerable**) mediante clustering (K-means), de
forma que la toma de decisiones ante un evento, o la inversión preventiva
en respaldo, se pueda ordenar por impacto financiero real y no solo por
criterio logístico.

### ¿A quiénes impacta?

* **Equipo de Riesgo y Control Interno**: insumo directo para los planes
de continuidad del negocio (PCN) y la actualización de la matriz BCP.
* **Planificación de inversión**: identificar qué estaciones críticas
carecen de respaldo eléctrico o conectividad, y priorizar ahí la
inversión en vez de distribuirla de forma pareja.





### Alcance

**Qué considera:**

* Las 336 estaciones de servicio del archivo `Data\_ptos\_operativos\_PCN.xlsx`.
* Variables de ventas, Tier operativo, respaldo eléctrico (GE) y
conectividad de respaldo (Starlink).
* Impacto financiero por **hora** de interrupción.



## 2\. Planificación del trabajo

### Descriptor de fases

|Fase|Entregable|Contenido|
|-|-|-|
|1|Entrega 01|Descripción del problema + planificación|
|2|Entrega 02|MVP: script R que calcula el índice de criticidad y lo visualiza|
|3|Entrega 03|Documentación (model card), plan de despliegue y plan de monitoreo|



