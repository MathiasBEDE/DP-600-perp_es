# Caso de estudio 1: Plataforma de Retail Analytics

## Escenario

NorthStar Retail es una empresa minorista mediana que opera **200 tiendas** en cuatro regiones de EE. UU. (Northeast, Southeast, Midwest y West). La empresa vende aproximadamente **5,000 SKUs** que abarcan electrónica, ropa, artículos para el hogar y comestibles. Actualmente, NorthStar ejecuta un data warehouse SQL Server 2019 on-premises alojado en un centro de datos co-ubicado. El warehouse ingiere cargas batch nocturnas desde sistemas de punto de venta (POS), una aplicación de administración de inventario, una base de datos del programa de fidelización de clientes y exportaciones CSV de web analytics.

La plataforma existente sufre varios problemas. Los trabajos ETL nocturnos exceden con frecuencia su ventana de mantenimiento de seis horas, lo que provoca reports matutinos con datos obsoletos. Los gerentes regionales dependen de extractos de Excel enviados por correo en lugar de dashboards de autoservicio. El equipo de data engineering de tres personas dedica la mayor parte de su tiempo a solucionar problemas de paquetes SSIS en vez de construir nueva analítica. Los costos de almacenamiento aumentan porque el equipo conserva snapshots históricos completos en lugar de cargas incrementales.

El CTO de NorthStar aprobó una migración a **Microsoft Fabric**. Los objetivos del proyecto son:

1. **Seguimiento de inventario casi en tiempo real** — las posiciones de inventario deben actualizarse dentro de los 15 minutos posteriores a una transacción POS.
2. **Reporting diario de ventas** — dashboards ejecutivos actualizados antes de las 6:00 AM hora local cada día.
3. **Pronóstico de demanda estacional** — pronósticos mensuales impulsados por ML consumidos por el equipo de merchandising en Power BI.
4. **Self-service analytics** — los gerentes regionales deben poder explorar mediante Power BI solo los datos relevantes para su región, sin ver datos de otras regiones.
5. **Optimización de costos** — la Fabric capacity debe mantenerse dentro de un presupuesto de SKU F64, excepto durante picos estacionales (Black Friday, regreso a clases), cuando puede hacer `burst` a F128.

El equipo de analytics engineering está compuesto por dos data engineers, un analytics engineer, un desarrollador de Power BI y un data steward de medio tiempo. Planean un despliegue por fases: la Fase 1 cubre ventas e inventario; la Fase 2 agrega fidelización y web analytics; la Fase 3 introduce el modelo de forecasting.

Los volúmenes diarios de datos son aproximadamente **2.5 millones de transacciones POS**, **400,000 registros de movimiento de inventario**, **120,000 eventos de fidelización** y **50 GB de archivos CSV de web analytics**. Deben migrarse datos históricos de cinco años (aproximadamente 3 TB comprimidos en Parquet).

---

## Diagrama de arquitectura

```
┌──────────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                                  │
│                                                                      │
│  ┌─────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐│
│  │ POS Systems  │ │  Inventory   │ │  Customer    │ │ Web Analytics││
│  │ (SQL Server) │ │  (REST API)  │ │  Loyalty     │ │ (Azure Blob) ││
│  │              │ │              │ │  (Azure SQL) │ │  CSV files   ││
│  └──────┬───────┘ └──────┬───────┘ └──────┬───────┘ └──────┬───────┘│
└─────────┼────────────────┼────────────────┼────────────────┼─────────┘
          │                │                │                │
          ▼                ▼                ▼                ▼
┌──────────────────────────────────────────────────────────────────────┐
│                     MICROSOFT FABRIC                                 │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                    INGESTION LAYER                             │  │
│  │  Data Pipeline (POS, Loyalty)  │  Dataflow Gen2 (Inventory)   │  │
│  │  Shortcut (Azure Blob CSVs)                                   │  │
│  └────────────────────────┬───────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                   MEDALLION LAKEHOUSE                         │  │
│  │                                                               │  │
│  │  ┌──────────┐    ┌──────────┐    ┌──────────┐                │  │
│  │  │  BRONZE  │───▶│  SILVER  │───▶│   GOLD   │                │  │
│  │  │ (raw)    │    │ (cleaned)│    │ (curated)│                │  │
│  │  │ Delta    │    │ Delta    │    │ Delta    │                │  │
│  │  └──────────┘    └──────────┘    └──────────┘                │  │
│  └────────────────────────┬───────────────────────────────────────┘  │
│                           ▼                                          │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │               SEMANTIC LAYER & CONSUMPTION                    │  │
│  │                                                               │  │
│  │  ┌───────────────┐   ┌──────────────┐   ┌─────────────────┐  │  │
│  │  │ Semantic Model │   │  Power BI     │   │  ML Forecasting │  │  │
│  │  │ (Direct Lake)  │   │  Reports      │   │  (Notebook)     │  │  │
│  │  │ + RLS roles    │   │  + Dashboards │   │                 │  │  │
│  │  └───────────────┘   └──────────────┘   └─────────────────┘  │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Requisitos

- **Medallion architecture** con capas Bronze (ingesta raw), Silver (limpia y conformada) y Gold (agregados de nivel de negocio) en un Fabric Lakehouse usando tablas Delta.
- **Inventario casi en tiempo real** mediante Eventstream o un pipeline micro-batch que se ejecute con una programación de 15 minutos.
- **Carga incremental de datos** para datos POS y de fidelización, reemplazando snapshots nocturnos completos.
- **Direct Lake semantic model** conectado a tablas Delta de la capa Gold para rendimiento óptimo de consultas.
- **Row-Level Security (RLS)** en el semantic model para que cada gerente regional vea solo su región.
- **Deployment pipelines** (Dev → Test → Prod) para todos los artefactos de Fabric.
- **Controles de calidad de datos** en la capa Silver usando validación basada en Notebooks o transformaciones con Dataflow Gen2.
- **Auto-scaling de capacity** durante períodos pico de retail con monitoreo mediante Fabric Capacity Metrics app.

---

## Fuentes de datos

| Fuente | Sistema | Protocolo | Volumen (diario) | Cadencia de refresh |
|---|---|---|---|---|
| Point-of-Sale | SQL Server 2019 (on-prem) | On-premises data gateway | ~2.5 M filas | Micro-batch de 15 min |
| Inventory | Inventory Management App | REST API (JSON) | ~400 K registros | Micro-batch de 15 min |
| Customer Loyalty | Azure SQL Database | Conexión directa | ~120 K eventos | Horaria |
| Web Analytics | Azure Blob Storage | Shortcut / ADLS Gen2 | ~50 GB CSV | Batch diario |

---

## Restricciones y consideraciones

- **Presupuesto:** Capacity F64 en estado estable; `burst` a F128 durante Nov–Dec y Jul–Aug.
- **SLA:** Dashboards ejecutivos disponibles a las 06:00 hora local con 99.5 % de uptime.
- **Retención de datos:** Cinco años de historial transaccional; agregados retenidos indefinidamente.
- **Seguridad:** Autenticación con Azure AD; los gerentes regionales no deben ver datos de otras regiones.
- **Cumplimiento:** PCI-DSS para datos de tarjetas de pago; los números de tarjeta deben enmascararse o tokenizarse antes de aterrizar en Bronze.
- **Equipo:** 2 data engineers, 1 analytics engineer, 1 desarrollador de Power BI, 1 data steward de medio tiempo.
- **Retiro del legado:** El warehouse SQL Server on-prem debe ejecutarse en paralelo durante 90 días después del go-live.

---

## Preguntas

### Pregunta 1

Los data engineers de NorthStar necesitan ingerir datos de transacciones POS desde SQL Server on-premises hacia la capa Bronze del Fabric Lakehouse cada 15 minutos. La red on-premises no tiene un private endpoint directo hacia Microsoft Fabric. ¿Qué enfoque deberían usar?

- A) Crear un Dataflow Gen2 que se conecte directamente a SQL Server por internet público usando autenticación SQL.
- B) Instalar un on-premises data gateway y configurar un Fabric Data Pipeline con una Copy activity usando un trigger de ventana acumulativa de 15 minutos.
- C) Exportar datos POS a archivos CSV, cargarlos manualmente en Azure Blob Storage y crear un shortcut en el Lakehouse.
- D) Usar Fabric Eventstream con una conexión JDBC directa a SQL Server.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Instalar un on-premises data gateway y configurar un Fabric Data Pipeline con una Copy activity usando un trigger de ventana acumulativa de 15 minutos.**

**Explicación:** Se requiere un on-premises data gateway para conectar SQL Server on-prem con Microsoft Fabric cuando no existe un private endpoint. Un Data Pipeline con una Copy activity admite triggers programados, incluidos los de ventana acumulativa, para ingesta micro-batch. La opción A es insegura y requeriría exponer SQL Server a internet. La opción C es un proceso manual que no puede cumplir la frescura de 15 minutos. La opción D no es un patrón soportado: Eventstream está diseñado para fuentes de streaming como Azure Event Hubs o Kafka, no para conexiones JDBC directas a bases de datos relacionales.

</details>

---

### Pregunta 2

El analytics engineer está diseñando la transformación de la capa Silver para datos POS. La tabla Bronze contiene payloads JSON raw con arreglos anidados para líneas de detalle. Algunos registros llegan con valores `store_id` nulos por fallas del sistema POS. ¿Qué combinación de acciones es la MÁS apropiada para la capa Silver?

- A) Dejar los datos tal como están en formato JSON anidado y manejar el aplanamiento en el semantic model usando DAX.
- B) Usar un Fabric Notebook con PySpark para aplanar los arreglos anidados en un esquema relacional y descartar todas las filas donde `store_id` sea nulo.
- C) Usar un Fabric Notebook con PySpark para aplanar los arreglos anidados, poner en cuarentena las filas con `store_id` nulo en una tabla de errores de calidad de datos, y escribir las filas válidas en la tabla Delta Silver.
- D) Usar Dataflow Gen2 para aplanar el JSON y reemplazar los valores `store_id` nulos por un valor predeterminado `"UNKNOWN"`.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Usar un Fabric Notebook con PySpark para aplanar los arreglos anidados, poner en cuarentena las filas con `store_id` nulo en una tabla de errores de calidad de datos, y escribir las filas válidas en la tabla Delta Silver.**

**Explicación:** El propósito de la capa Silver es limpiar y conformar datos. Aplanar estructuras anidadas es esencial para la usabilidad downstream. Descartar silenciosamente registros inválidos (opción B) pierde datos e impide investigar problemas. Reemplazar nulos con `"UNKNOWN"` (opción D) oculta problemas de calidad de datos y puede producir analítica engañosa. Dejar JSON raw para manejarlo con DAX (opción A) es una mala práctica que empuja la complejidad de transformación a la capa semántica. Poner en cuarentena registros inválidos los conserva para investigación mientras mantiene limpia la tabla Silver.

</details>

---

### Pregunta 3

El desarrollador de Power BI necesita implementar Row-Level Security para que cada uno de los cuatro gerentes regionales vea solo los datos de su propia región. El semantic model usa modo Direct Lake conectado a la capa Gold. ¿Cuál es el enfoque correcto de implementación?

- A) Crear cuatro reports de Power BI separados, cada uno con un filtro hard-coded para una región.
- B) Definir roles RLS en el semantic model usando expresiones de filtro DAX sobre la tabla de dimensión Region y asignar la cuenta de Azure AD de cada gerente regional al rol correspondiente.
- C) Crear cuatro tablas Delta Gold separadas, una por región, y construir cuatro semantic models separados.
- D) Usar object-level security (OLS) para ocultar la columna Region a gerentes que no deban ver otras regiones.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Definir roles RLS en el semantic model usando expresiones de filtro DAX sobre la tabla de dimensión Region y asignar la cuenta de Azure AD de cada gerente regional al rol correspondiente.**

**Explicación:** Row-Level Security en semantic models de Power BI / Fabric se implementa creando roles con expresiones de filtro DAX, por ejemplo `[Region] = "Northeast"`, y asignando usuarios a roles. Este es el enfoque estándar y escalable. Crear reports separados (A) o modelos separados (C) genera sobrecarga de mantenimiento y contradice el objetivo de un modelo unificado. OLS (D) oculta columnas o tablas completas; no filtra filas y no resuelve el requisito de mostrar datos específicos por región.

</details>

---

### Pregunta 4

Durante las pruebas de la Fase 1, el equipo descubre que el Direct Lake semantic model ocasionalmente cae a modo DirectQuery durante horas pico, causando bajo rendimiento en los reports. ¿Cuál es la causa MÁS probable y cómo deberían abordarla?

- A) Las tablas Delta tienen demasiados archivos Parquet pequeños; deberían ejecutar una operación `OPTIMIZE` (bin-compaction) sobre las tablas de la capa Gold.
- B) El modelo Direct Lake no soporta cálculos DAX; deberían eliminar todas las medidas.
- C) Los reports de Power BI están usando modo Import; deberían cambiarlos a Direct Lake.
- D) La Fabric capacity está pausada; deberían reanudarla.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Las tablas Delta tienen demasiados archivos Parquet pequeños; deberían ejecutar una operación `OPTIMIZE` (bin-compaction) sobre las tablas de la capa Gold.**

**Explicación:** El modo Direct Lake lee archivos Delta/Parquet directamente en el motor de Analysis Services. Cuando las tablas tienen demasiados archivos pequeños, el problema conocido como `"small-file problem"`, el modelo puede exceder límites de framing y caer a DirectQuery, que es significativamente más lento. Ejecutar `OPTIMIZE` compacta archivos pequeños en archivos más grandes y también puede aplicar ordenamiento V-Order, manteniendo el modelo dentro de sus guardrails de framing. La opción B es incorrecta: Direct Lake soporta medidas DAX. Las opciones C y D describen problemas no relacionados.

</details>

---

### Pregunta 5

El data steward debe asegurar que se mantenga el cumplimiento PCI-DSS para datos de tarjetas de pago. Los números de tarjeta de crédito están presentes en los feeds POS raw. ¿En qué capa deberían enmascararse o tokenizarse los números de tarjeta, y cuál es el enfoque recomendado?

- A) Enmascarar los números de tarjeta en la capa Gold para conservar todos los datos raw históricos para auditores.
- B) Tokenizar los números de tarjeta antes de que aterricen en la capa Bronze aplicando la transformación en el mapeo de la Copy activity del pipeline de ingesta.
- C) Almacenar los números de tarjeta en texto plano en todas las capas, pero restringir el acceso usando roles de workspace.
- D) Enmascarar los números de tarjeta en el report de Power BI usando una medida DAX que reemplace dígitos por asteriscos.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Tokenizar los números de tarjeta antes de que aterricen en la capa Bronze aplicando la transformación en el mapeo de la Copy activity del pipeline de ingesta.**

**Explicación:** PCI-DSS requiere proteger los datos de tarjetahabientes en el punto más temprano posible del flujo de datos. Tokenizar o enmascarar durante la ingesta, antes de que los datos persistan en el Lakehouse, minimiza el alcance PCI de toda la plataforma. Almacenar números de tarjeta raw en Bronze (opción A) amplía el alcance PCI a cada capa que toca datos Bronze. Depender solo de roles de workspace (opción C) no satisface requisitos de cifrado/tokenización de PCI. El enmascaramiento a nivel de DAX (opción D) es un truco de capa de presentación que no protege los datos subyacentes en reposo.

</details>

---

### Pregunta 6

NorthStar quiere implementar deployment pipelines para sus artefactos de Fabric. El equipo necesita promover cambios desde workspaces de Development a Test y luego a Production. ¿Qué conjunto de artefactos puede incluirse en un Fabric deployment pipeline?

- A) Solo reports y semantic models de Power BI.
- B) Semantic models, reports, paginated reports, Lakehouses, Data Pipelines, Dataflows Gen2 y Notebooks.
- C) Solo Lakehouses y Notebooks.
- D) Todos los items de Fabric, incluidos Eventstreams, KQL Databases y Fabric capacities.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Semantic models, reports, paginated reports, Lakehouses, Data Pipelines, Dataflows Gen2 y Notebooks.**

**Explicación:** Los Fabric deployment pipelines soportan un conjunto amplio de tipos de item, incluidos semantic models (`datasets`), reports, paginated reports, dataflows, data pipelines, Lakehouses y Notebooks. No todos los tipos de item de Fabric están soportados; por ejemplo, Eventstreams y KQL databases tienen soporte limitado o nulo en deployment pipelines en este momento. Las Fabric capacities son recursos administrativos, no artefactos desplegables. La opción A es demasiado limitada, con una visión heredada solo de Power BI, y la opción C excluye items críticos como semantic models y pipelines.

</details>

---

### Pregunta 7

Los data engineers están diseñando la estrategia de carga incremental para la tabla de transacciones POS. La tabla origen de SQL Server tiene una columna `transaction_id` (BIGINT) monótonamente creciente y una columna `transaction_timestamp` (DATETIME2). ¿Cuál es el MEJOR enfoque para la ingesta incremental hacia la capa Bronze?

- A) Realizar una extracción completa de la tabla cada 15 minutos y sobrescribir la tabla Delta Bronze.
- B) Usar un patrón de watermark en el Data Pipeline: almacenar el último `transaction_id` cargado en una tabla de watermark y consultar solo filas con `transaction_id` mayor al watermark en cada ejecución.
- C) Usar Change Data Capture (CDC) en el SQL Server origen y configurar el Data Pipeline para leer desde las tablas CDC, capturando inserts, updates y deletes.
- D) Exportar toda la tabla a Parquet una vez al día y usar un shortcut.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Usar Change Data Capture (CDC) en el SQL Server origen y configurar el Data Pipeline para leer desde las tablas CDC, capturando inserts, updates y deletes.**

**Explicación:** CDC es la estrategia incremental más robusta porque captura todos los tipos de cambio (inserts, updates, deletes) directamente desde el transaction log, garantizando que no se pierda nada, incluso correcciones tardías o transacciones anuladas. Un enfoque con watermark (opción B) maneja bien inserts, pero puede perder updates sobre filas ya cargadas, por ejemplo una transacción anulada. Las extracciones completas (opción A) desperdician ancho de banda de red y `compute` para 2.5 M filas cada 15 minutos. Las exportaciones diarias a Parquet (opción D) no cumplen el SLA de frescura de 15 minutos.

</details>

---

### Pregunta 8

El analytics engineer está construyendo la tabla agregada `fact_daily_sales` de la capa Gold. La tabla debe soportar consultas rápidas de dashboards para ventas por tienda, categoría de producto y fecha. ¿Qué estrategias de optimización debería aplicar a la tabla Delta? (Elige DOS).

- A) Habilitar V-Order en la tabla Delta para optimizarla para el motor Analysis Services en modo Direct Lake.
- B) Particionar la tabla por `transaction_id` para maximizar el paralelismo.
- C) Usar Z-Order (`OPTIMIZE ZORDER`) sobre las columnas `store_id` y `category_id` para mejorar la eficiencia de data-skipping.
- D) Almacenar la tabla como CSV en lugar de Delta para reducir costos de almacenamiento.
- E) Deshabilitar toda recopilación de estadísticas para reducir la sobrecarga de escritura.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Habilitar V-Order en la tabla Delta para optimizarla para el motor Analysis Services en modo Direct Lake Y C) Usar Z-Order (`OPTIMIZE ZORDER`) sobre las columnas `store_id` y `category_id` para mejorar la eficiencia de data-skipping.**

**Explicación:** V-Order es una optimización específica de Fabric en tiempo de escritura que organiza los datos dentro de archivos Parquet para rendimiento óptimo de lectura por parte del motor Analysis Services que impulsa Direct Lake; se recomienda especialmente para tablas Gold consumidas por semantic models. Z-Order coloca conjuntamente datos de columnas especificadas dentro de los mismos grupos de archivos, lo que permite que las estadísticas de data-skipping de Delta eliminen archivos irrelevantes durante consultas filtradas por tienda o categoría. Particionar por `transaction_id` (B) crea millones de particiones pequeñas y es un antipatrón. CSV (D) pierde funcionalidades Delta como transacciones ACID y time travel. Deshabilitar estadísticas (E) degrada el rendimiento de consultas.

</details>

---

### Pregunta 9

Durante la planificación de capacity, el equipo necesita decidir cómo manejar el aumento de F64 a F128 durante el período de Black Friday. ¿Qué funcionalidad o práctica de Fabric deberían usar para administrarlo?

- A) Eliminar manualmente Lakehouses no usados para liberar Capacity Units antes de Black Friday.
- B) Escalar el SKU de Fabric capacity de F64 a F128 en Azure portal antes del período pico y volver a reducirlo después.
- C) Crear una segunda capacity F64 y dividir las cargas de trabajo entre ambas capacities permanentemente.
- D) Cambiar todos los semantic models de Direct Lake a modo Import para reducir consumo de capacity.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Escalar el SKU de Fabric capacity de F64 a F128 en Azure portal antes del período pico y volver a reducirlo después.**

**Explicación:** Las Fabric capacities pueden escalarse hacia arriba o hacia abajo en Azure portal, o mediante Azure Resource Manager APIs / automatización. Escalar a F128 durante períodos pico conocidos y volver a F64 después es el enfoque rentable y operacionalmente correcto. Eliminar Lakehouses (A) elimina datos y no es una estrategia de administración de capacity. Dividir cargas entre dos capacities de forma permanente (C) duplica los costos en estado estable. Cambiar a modo Import (D) en realidad aumentaría el consumo de capacity porque los refreshes de Import consumen mucho `compute`, y además se perderían los beneficios de Direct Lake.

</details>

---

### Pregunta 10

El equipo completó la Fase 1 y se está preparando para la Fase 3: forecasting de demanda estacional. Planean entrenar un modelo de ML en un Fabric Notebook y mostrar predicciones en Power BI. ¿Qué enfoque permite que el equipo de merchandising consuma los datos de forecast en sus dashboards existentes de Power BI con el menor esfuerzo?

- A) Exportar las predicciones del modelo a un archivo CSV y enviarlo por correo al equipo de merchandising semanalmente.
- B) Escribir los resultados de predicción en una tabla Delta de la capa Gold en el Lakehouse y agregar la tabla al semantic model Direct Lake existente como una nueva tabla de forecast.
- C) Construir una API Python Flask independiente que sirva predicciones y conectar Power BI a ella mediante un web connector.
- D) Almacenar las predicciones en un workspace separado de Azure Machine Learning y exigir al equipo de merchandising usar Azure ML Studio para visualización.

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Escribir los resultados de predicción en una tabla Delta de la capa Gold en el Lakehouse y agregar la tabla al semantic model Direct Lake existente como una nueva tabla de forecast.**

**Explicación:** Escribir predicciones de vuelta en la capa Gold como una tabla Delta integra los forecasts de forma natural en la medallion architecture existente. Agregar la tabla al semantic model Direct Lake significa que el equipo de merchandising puede explorar forecasts en los mismos dashboards de Power BI que ya utiliza, sin nuevas herramientas ni flujos de trabajo. La opción A es un proceso manual y no escalable. La opción C introduce infraestructura innecesaria fuera de Fabric. La opción D obliga a usuarios finales a usar otra plataforma, incumpliendo el objetivo de self-service analytics.

</details>
