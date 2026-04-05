# Dominio 1: Planear, implementar y administrar una solución de analítica de datos (10–15%)

> **Ponderación del examen:** Este dominio representa entre el 10–15% del examen DP-600 Implementing Analytics Solutions Using Microsoft Fabric. Aunque es el dominio con menor ponderación, establece el conocimiento fundamental requerido para todos los demás dominios.

---

## Tabla de contenido

1. [Diseñar y definir la arquitectura de soluciones analíticas en Microsoft Fabric](#1-diseñar-y-definir-la-arquitectura-de-soluciones-analíticas-en-microsoft-fabric)
2. [Administrar y configurar Workspaces y entornos](#2-administrar-y-configurar-workspaces-y-entornos)
3. [Aplicar gobernanza, seguridad y cumplimiento](#3-aplicar-gobernanza-seguridad-y-cumplimiento)
4. [Roles y permisos de Workspace](#4-roles-y-permisos-de-workspace)
5. [Supervisión y auditoría del uso](#5-supervisión-y-auditoría-del-uso)
6. [Data lineage y análisis de impacto](#6-data-lineage-y-análisis-de-impacto)
7. [Administrar Capacity y la asignación de recursos](#7-administrar-capacity-y-la-asignación-de-recursos)

---

## 1. Diseñar y definir la arquitectura de soluciones analíticas en Microsoft Fabric

### 1.1 Descripción general y componentes de Microsoft Fabric

Microsoft Fabric es una plataforma unificada de analítica de extremo a extremo que reúne data engineering, data integration, data warehousing, data science, real-time analytics y business intelligence en una sola experiencia SaaS construida sobre una base compartida llamada **OneLake**.

#### Componentes principales (experiences)

| Experience | Descripción | Persona principal |
|---|---|---|
| **Data Factory** | Integración y orquestación de datos (`pipelines`, `dataflows`) | Data Engineer |
| **Synapse Data Engineering** | Lakehouse, notebooks de Spark, definiciones de trabajos de Spark | Data Engineer |
| **Synapse Data Warehouse** | data warehousing basado en T-SQL | Data Warehouse Developer |
| **Synapse Data Science** | Modelos de ML, experimentos, notebooks | Data Scientist |
| **Synapse Real-Time Analytics** | Bases de datos KQL, `eventstreams`, dashboards en tiempo real | Data Analyst / Engineer |
| **Power BI** | Informes, dashboards, semantic models (`datasets`) | Business Analyst |

#### OneLake — La base

OneLake es el data lake unificado que sustenta todo Microsoft Fabric. Características clave:

- **Copia única de los datos** — todas las cargas de trabajo de Fabric leen y escriben en OneLake, eliminando los silos de datos
- **Construido sobre Azure Data Lake Storage Gen2 (ADLS Gen2)** internamente
- **Namespace jerárquico** — los datos se organizan por tenant → workspace → item (`lakehouse`, `warehouse`, etc.)
- **Formato abierto de forma predeterminada** — los datos se almacenan en formato **Delta Parquet** (Delta Lake)
- **Shortcuts de OneLake** — permiten hacer referencia a datos en almacenamiento externo (ADLS Gen2, Amazon S3, Google Cloud Storage, Dataverse) sin copiarlos

> **Exam Tip:** Comprende que OneLake es un único lago lógico por tenant. NO creas múltiples lagos; organizas los datos dentro de workspaces e items.

#### Terminología clave

| Término | Definición |
|---|---|
| **Tenant** | El límite organizativo de nivel superior en Fabric (corresponde a un tenant de Azure AD / Entra ID) |
| **Capacity** | El conjunto de cómputo y recursos asignado para ejecutar cargas de trabajo de Fabric, medido en Capacity Units (CUs) |
| **Workspace** | Un contenedor lógico para elementos de Fabric que proporciona límites de colaboración y control de acceso basado en roles (`Admin`, `Member`, `Contributor`, `Viewer`) |
| **Item** | Cualquier artefacto dentro de un workspace (`lakehouse`, `warehouse`, notebook, pipeline, report, semantic model, etc.) |
| **OneLake** | La capa de almacenamiento unificada para todos los datos de Fabric |
| **Shortcut** | Un puntero a datos almacenados en otra ubicación (interna o externa) sin copiar los datos |

### 1.2 Patrones de arquitectura de soluciones

Al diseñar soluciones analíticas en Fabric, considera estos patrones comunes:

#### Arquitectura Medallion (Bronze → Silver → Gold)

El patrón más recomendado para organizar datos en un Lakehouse de Fabric:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Bronze     │────▶│   Silver     │────▶│    Gold      │
│  (Raw Data)  │     │ (Cleansed)   │     │ (Curated)    │
└─────────────┘     └─────────────┘     └─────────────┘
   Ingestion          Transformation       Serving
   as-is              validated            aggregated
   full fidelity      conformed            business-ready
```

- **Bronze (Raw):** Aterriza los datos tal como vienen de los sistemas origen. Conserva la fidelidad completa.
- **Silver (Cleansed):** Aplica reglas de calidad de datos, imposición de esquema, deduplicación y `joins`.
- **Gold (Curated):** Agregaciones de nivel de negocio, `star schemas` y métricas listas para informes.

> **Best Practice:** Implementa Bronze → Silver → Gold usando `lakehouses` o `schemas` separados dentro de un mismo `lakehouse`, conectados mediante `shortcuts` o notebooks de Spark.

#### Arquitectura Hub-and-Spoke

Para implementaciones empresariales con múltiples unidades de negocio:

- **Hub workspace:** Workspace central de data engineering con `datasets` compartidos, `master data` y controles de gobernanza
- **Spoke workspaces:** Workspaces específicos de cada unidad de negocio que consumen datos desde el hub mediante `shortcuts` o `semantic models` compartidos

#### Data Mesh

Para organizaciones altamente descentralizadas:

- Cada equipo de dominio es propietario y publica sus `data products`
- Los `shortcuts` de OneLake permiten compartir datos entre dominios sin duplicación
- Las características de `endorsement` y `data catalog` ayudan a los consumidores a descubrir datos confiables

### 1.3 Elegir el compute adecuado

Una de las decisiones arquitectónicas más críticas en Fabric es elegir entre **Lakehouse**, **Warehouse** y **Dataflow Gen2**.

#### Lakehouse vs. Warehouse vs. Dataflow Gen2

| Característica | Lakehouse | Warehouse | Dataflow Gen2 |
|---|---|---|---|
| **Formato de almacenamiento** | Delta Lake (Parquet) | Delta Lake (Parquet) | N/A (motor de procesamiento) |
| **Lenguaje de consulta** | Spark (PySpark, SparkSQL, Scala, R) + T-SQL (mediante SQL analytics endpoint) | T-SQL | Power Query M |
| **Schema** | `Schema-on-read` (flexible) | `Schema-on-write` (forzado) | Schema definido en el flujo |
| **Ideal para** | data engineering, big data, ML, datos no estructurados/semiestructurados | data warehousing tradicional, consultas SQL complejas, procedimientos almacenados | ETL low-code/no-code, transformaciones simples |
| **Soporte de escritura** | Completo (Spark, APIs) | Completo (T-SQL DML: `INSERT`, `UPDATE`, `DELETE`, `MERGE`) | Escritura en `lakehouse` o `warehouse` como destino |
| **Persona principal** | Data Engineer, Data Scientist | Data Warehouse Developer, SQL Analyst | Citizen Integrator, Power BI Developer |
| **Optimización V-Order** | Sí | Sí | N/A |
| **Consultas cross-database** | Sí (mediante SQL analytics endpoint) | Sí (SQL cross-database) | No |

> **Exam Tip:** El Lakehouse tiene un **SQL analytics endpoint** que proporciona una interfaz T-SQL de solo lectura sobre las tablas Delta. NO puedes escribir en un Lakehouse mediante T-SQL; para escribir se requiere Spark o APIs.

#### Framework de decisión

```
¿Necesitas escribir datos con T-SQL (INSERT/UPDATE/DELETE)?
  ├── SÍ → Usa Warehouse
  └── NO
       ├── ¿Necesitas Spark para el procesamiento (PySpark, ML)?
       │    ├── SÍ → Usa Lakehouse
       │    └── NO
       │         ├── ¿Es una transformación simple, low-code?
       │         │    ├── SÍ → Usa Dataflow Gen2
       │         │    └── NO → Usa Lakehouse o Warehouse
       └── ¿Tienes datos no estructurados/semiestructurados?
            ├── SÍ → Usa Lakehouse (sección Files)
            └── NO → Cualquiera funciona; considera las habilidades del equipo
```

### 1.4 Planificación de Capacity y selección de SKU

Las capacidades de Fabric se miden en **Capacity Units (CUs)**, que determinan la potencia de `compute` disponible para todas las cargas de trabajo.

#### SKUs de Fabric

| SKU | Capacity Units (CUs) | Equivalente en Power BI | Memoria máxima por consulta | Adecuado para |
|---|---|---|---|---|
| F2 | 2 | — | 3 GB | Dev/Test, POC |
| F4 | 4 | — | 3 GB | Equipos pequeños |
| F8 | 8 | EM/A1 | 3 GB | Cargas de trabajo pequeñas |
| F16 | 16 | EM2/A2 | 6 GB | Cargas de trabajo medianas |
| F32 | 32 | EM3/A3 | 6 GB | Nivel departamental |
| F64 | 64 | P1/A4 | 25 GB | Enterprise |
| F128 | 128 | P2/A5 | 50 GB | Enterprise grande |
| F256 | 256 | P3/A6 | 100 GB | Enterprise grande |
| F512 | 512 | P4/A7 | 200 GB | Enterprise muy grande |
| F1024 | 1024 | P5/A8 | 400 GB | Mission-critical |
| F2048 | 2048 | — | 400 GB | Las cargas de trabajo más grandes |

> **Note:** Las capacidades de Fabric Trial proporcionan un equivalente a F64 durante 60 días. Las cargas de trabajo de producción requieren una capacidad de pago.

#### Conceptos clave de Capacity

- **Bursting:** Fabric permite un `bursting` a corto plazo por encima de tu asignación de CU, aplicando `throttling` cuando se sostiene en el tiempo
- **Smoothing:** Fabric suaviza el uso de `compute` en ventanas de tiempo para evitar `throttling` repentino
- **Throttling:** Cuando se excede la capacidad de forma constante, las operaciones interactivas se retrasan
- **Autoscale:** Puede configurarse para agregar automáticamente CUs durante picos de uso (costo adicional)

> **Best Practice:** Comienza con un SKU más pequeño (F64) para desarrollo y escala hacia arriba según los patrones de uso reales observados en la aplicación Capacity Metrics.

**Referencia:** [Microsoft Fabric capacity and SKUs](https://learn.microsoft.com/en-us/fabric/enterprise/licenses)

---

## 2. Administrar y configurar Workspaces y entornos

### 2.1 Creación y configuración de Workspaces

Un **workspace** es la unidad organizativa principal en Fabric. Sirve como contenedor para `items` y proporciona un límite de colaboración.

#### Crear un Workspace

1. Ve al portal de Fabric (`app.fabric.microsoft.com`)
2. Haz clic en **Workspaces** en el panel de navegación izquierdo
3. Haz clic en **+ New workspace**
4. Configura las siguientes opciones:
   - **Name:** Descriptivo, siguiendo la convención de nomenclatura de tu organización
   - **Description:** Propósito y contenido del workspace
   - **License mode:** Selecciona la capacity (Trial, Premium, Fabric o Embedded)
   - **Default storage format:** Normalmente se deja con el valor predeterminado (Delta/Parquet)
   - **Contact list:** Especifica a quién contactar sobre el workspace
   - **OneLake data access:** Configura las opciones de acceso a datos

> **Buena práctica:** Adopta una convención de nomenclatura coherente. Ejemplo:  
> `{BusinessUnit}_{Environment}_{Purpose}`  
> p. ej., `Finance_Prod_DataWarehouse`, `Marketing_Dev_Analytics`

#### Tipos de Workspace

| Tipo | Descripción | Caso de uso |
|---|---|---|
| **Personal workspace ("My workspace")** | Workspace privado para cada usuario | Exploración individual, informes personales |
| **Shared workspace** | Workspace colaborativo asignado a una capacity | Proyectos de equipo, cargas de trabajo de producción |
| **Admin monitoring workspace** | Workspace especial del sistema para monitoreo a nivel de tenant | Administradores del tenant que supervisan el uso |

> **Consejo de examen:** `"My workspace"` NO admite todas las funcionalidades de Fabric y NO debe usarse para cargas de trabajo de producción. Los `items` en `"My workspace"` no se pueden compartir con la misma facilidad y no admiten roles de workspace.

### 2.2 Configuración y propiedades del Workspace

Configuraciones clave del workspace que debes comprender:

#### Asignación de licencia / Capacity

- Cada workspace debe asignarse a una capacity para usar las funcionalidades de Fabric
- Los workspaces en capacity compartida (Pro) tienen funcionalidades limitadas
- Los `items` de Fabric (`Lakehouse`, `Warehouse`, etc.) requieren como mínimo una capacity F64 o equivalente

#### Integración con Azure DevOps / Git

- Conecta el workspace a un repositorio de Azure DevOps o GitHub
- Habilita control de versiones para los `items` del workspace
- Admite estrategias de `branching` para flujos de desarrollo

#### Configuración de Spark (nivel Workspace)

- Configuración predeterminada del pool de Spark
- `Environment` (bibliotecas, propiedades de Spark) para notebooks y definiciones de trabajos de Spark

#### Asignación de dominio

- Asocia un workspace con un dominio de Fabric para gobernanza y descubrimiento
- Los dominios proporcionan una agrupación lógica entre workspaces para escenarios de `data mesh`

### 2.3 Configuración de Environment

Los **Environments** en Fabric definen la configuración de ejecución para los `items` basados en Spark (notebooks, definiciones de trabajos de Spark).

#### Qué contiene un Environment

| Componente | Descripción |
|---|---|
| **Public libraries** | Paquetes de PyPI o Conda para instalar |
| **Custom libraries** | Archivos `.whl`, `.jar`, `.tar.gz` cargados |
| **Spark properties** | Sobrescrituras de configuración (p. ej., `spark.sql.shuffle.partitions`) |
| **Resources** | Archivos de datos cargados y accesibles desde sesiones de Spark |

#### Crear y adjuntar un Environment

```python
# Ejemplo: Usar un environment personalizado en un notebook
# 1. Crear el environment en el workspace
# 2. Agregar las librerías necesarias (p. ej., great_expectations, openpyxl)
# 3. Publicar el environment
# 4. Adjuntar el environment al notebook desde la barra de herramientas
```

Pasos:
1. En el workspace, haz clic en **+ New** → **Environment**
2. Agrega `public libraries` (desde PyPI/Conda) y/o carga `custom libraries`
3. Configura las `Spark properties` según sea necesario
4. Haz clic en **Publish** para compilar el environment
5. Adjunta el environment a un notebook o a una definición de trabajo de Spark

> **Nota:** Después de publicar, el proceso de compilación del environment tarda algunos minutos. Los conflictos entre bibliotecas se resuelven durante el paso de publicación.

### 2.4 Configuración de Spark Compute

El `Spark compute` de Fabric se administra mediante **Starter pools** y **Custom pools**.

#### Starter Pool (predeterminado)

- Pool precalentado para un inicio rápido de sesión
- Se asigna automáticamente según el SKU de la capacity
- Adecuado para la mayoría de los escenarios ad hoc y de desarrollo
- Proporciona nodos medianos con un número de nodos predeterminado

#### Custom Spark Pools

Para tener más control sobre los recursos de `compute`:

| Configuración | Opciones |
|---|---|
| **Node family** | Memory Optimized |
| **Node size** | Small, Medium, Large, XLarge, XXLarge |
| **Autoscale** | Habilitar/deshabilitar, nodos mínimos y máximos |
| **Dynamic allocation** | Habilitar/deshabilitar para ejecutores de Spark |

```
Workspace Settings → Data Engineering/Science → Spark Compute
├── Starter Pool (predeterminado, precalentado)
├── Custom Pool 1
│   ├── Node Size: Large
│   ├── Min Nodes: 1
│   ├── Max Nodes: 10
│   └── Autoscale: Enabled
└── Custom Pool 2
    ├── Node Size: Medium
    ├── Min Nodes: 2
    ├── Max Nodes: 5
    └── Autoscale: Enabled
```

> **Buena práctica:** Usa el Starter pool para desarrollo y exploración. Crea `custom pools` para cargas de trabajo de producción que necesiten rendimiento consistente y predecible.

### 2.5 Integración de Git para Workspaces

Fabric admite integración con Git para el control de versiones de los `items` del workspace.

#### Proveedores de control de código fuente admitidos

- **Azure DevOps (Azure Repos)**
- **GitHub**

#### Cómo funciona la integración con Git

1. Conecta el workspace a un repositorio Git (`branch`)
2. Los `items` de Fabric se serializan en archivos de definición dentro del repositorio
3. Los cambios se pueden confirmar (`commit`) desde el workspace hacia el repositorio
4. Los cambios del repositorio se pueden sincronizar (`pull`) hacia el workspace

#### Items compatibles con la integración de Git

La mayoría de los `items` de Fabric admiten integración con Git, incluidos:
- Notebooks
- Definiciones de trabajos de Spark
- `Lakehouses` (solo metadatos, no datos)
- `Warehouses` (solo metadatos, no datos)
- `Semantic models`
- Reports
- Pipelines
- Dataflows Gen2

#### Ejemplo de estrategia de branching

```
main (production)
  └── develop (staging/integration)
       ├── feature/add-sales-pipeline
       ├── feature/update-dim-customer
       └── bugfix/fix-date-transform
```

Cada `branch` se asigna a un workspace de Fabric:
- `main` → Workspace de producción
- `develop` → Workspace de staging
- `feature/*` → Workspaces específicos por desarrollador

> **Consejo de examen:** La integración con Git en Fabric rastrea las **definiciones** de los `items` (metadatos), no los datos reales almacenados en OneLake. Los datos no se versionan mediante integración con Git.

**Referencia:** [Introduction to Git integration](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/intro-to-git-integration)

---

## 3. Aplicar gobernanza, seguridad y cumplimiento

### 3.1 Gobernanza de datos en Fabric

La gobernanza de datos en Fabric garantiza que los datos se administren, protejan y usen adecuadamente en toda la organización.

#### Pilares de gobernanza en Fabric

| Pilar | Descripción | Funcionalidad de Fabric |
|---|---|---|
| **Discovery** | Encontrar y comprender los activos de datos | Data hub, data catalog, endorsement |
| **Protection** | Clasificar y proteger datos sensibles | Sensitivity labels, políticas DLP |
| **Compliance** | Cumplir requisitos normativos | Audit logs, integración con Purview |
| **Lineage** | Rastrear el flujo de datos y las dependencias | Lineage view, impact analysis |
| **Quality** | Garantizar precisión y consistencia de los datos | Data profiling, reglas de validación |

#### Dominios

Los **domains** de Fabric proporcionan una forma de agrupar lógicamente workspaces por área de negocio. Ayudan a implementar conceptos de `data mesh`:

- Son creados por administradores de Fabric
- Cada workspace puede asignarse a un dominio
- Los administradores del dominio pueden gestionar workspaces dentro de su dominio
- Permiten propiedad descentralizada mientras se mantiene supervisión central

```
Fabric Tenant
├── Domain: Finance
│   ├── Finance_Prod_Warehouse
│   ├── Finance_Dev_Lakehouse
│   └── Finance_Reports
├── Domain: Marketing
│   ├── Marketing_Analytics
│   └── Marketing_Campaigns
└── Domain: HR
    ├── HR_DataLake
    └── HR_Reports
```

### 3.2 Integración con Microsoft Purview

Microsoft Fabric se integra con **Microsoft Purview** para capacidades avanzadas de gobernanza.

#### Puntos clave de integración

| Funcionalidad | Descripción |
|---|---|
| **Information protection** | Aplicar `sensitivity labels` de Microsoft Purview Information Protection a los `items` de Fabric |
| **Data Loss Prevention** | Configurar políticas DLP para detectar datos sensibles en `items` de Fabric |
| **Data catalog** | Los `items` de Fabric se registran automáticamente en el `data catalog` de Purview |
| **Audit** | Las actividades de Fabric se capturan en el `unified audit log` (accesible mediante Purview) |
| **Data lineage** | Purview puede mostrar `lineage` entre Fabric y otras fuentes de datos |

> **Nota:** La integración con Microsoft Purview se configura a nivel de tenant por administradores de Fabric y Purview. Los usuarios individuales de workspace aprovechan estas funcionalidades, pero no las configuran.

### 3.3 Protección de la información y sensitivity labels

Las `sensitivity labels` de Microsoft Purview Information Protection se pueden aplicar a `items` de Fabric para clasificar y proteger datos sensibles.

#### Cómo funcionan las sensitivity labels en Fabric

1. Las **labels se definen** en el portal de cumplimiento de Microsoft Purview
2. Las **labels se aplican** a los `items` de Fabric (`reports`, `datasets`, `lakehouses`, etc.) manualmente o mediante automatización
3. Las **labels se heredan** — cuando los datos fluyen de un item a otro, la label puede propagarse `downstream`
4. Las **labels se aplican** — se aplican controles de exportación, cifrado y marcas visuales según la configuración de la label

#### Flujo de herencia de labels

```
Fuente de datos (etiquetada como "Confidential")
  → Lakehouse (hereda "Confidential")
    → Semantic Model (hereda "Confidential")
      → Report (hereda "Confidential")
        → PDF exportado (cifrado según la política de la label)
```

#### Sensitivity labels comunes

| Label | Descripción | Protección típica |
|---|---|---|
| **Public** | Datos no sensibles | Sin restricciones |
| **General** | Datos internos del negocio | Restricciones mínimas |
| **Confidential** | Datos sensibles del negocio | Cifrado, uso compartido restringido |
| **Highly Confidential** | Datos más sensibles | Cifrado fuerte, sin uso compartido externo |

> **Consejo de examen:** Las `sensitivity labels` en Fabric fluyen **downstream** a través del `data lineage`. Si un `lakehouse` de origen está etiquetado como `"Confidential"`, los `reports` downstream heredan esa label a menos que se sobrescriba con una label MÁS restrictiva.

### 3.4 Políticas de Data Loss Prevention (DLP)

Las políticas DLP en Fabric ayudan a evitar el uso compartido accidental de información sensible.

#### Escenarios DLP admitidos en Fabric

- Detectar tipos de datos sensibles (p. ej., números de seguro social, números de tarjeta de crédito) en **semantic models (`datasets`)**
- Detectar tipos de datos sensibles en **tablas de Lakehouse** (preview)
- Generar `policy tips` que advierten a los usuarios cuando se detectan datos sensibles
- Crear alertas para administradores cuando ocurren infracciones de DLP

#### Configuración de políticas DLP

Las políticas DLP para Fabric se configuran en el **portal de cumplimiento de Microsoft Purview**:

1. Ve a **Data Loss Prevention** → **Policies**
2. Crea una nueva política o usa una plantilla
3. Selecciona **Fabric** (y/o Power BI) como ubicación
4. Define los tipos de información sensible que se deben detectar
5. Configura acciones (`policy tips`, alertas, bloqueo)
6. Establece preferencias de notificación

> **Buena práctica:** Inicia las políticas DLP en **modo solo auditoría** antes de aplicarlas. Esto te permite evaluar el impacto sin interrumpir a los usuarios.

### 3.5 Consideraciones de cumplimiento y normativas

| Consideración | Capacidad de Fabric |
|---|---|
| **Data residency** | La capacity de Fabric se implementa en una región específica de Azure; los datos de OneLake residen en esa región |
| **Encryption at rest** | Habilitado de forma predeterminada usando claves administradas por Microsoft |
| **Encryption in transit** | TLS 1.2+ para todas las comunicaciones |
| **Audit trail** | Los `unified audit logs` capturan todas las actividades de Fabric |
| **Access reviews** | Integración con revisiones de acceso de Azure AD / Entra ID |
| **Conditional Access** | Las políticas de Entra ID Conditional Access se aplican a Fabric |
| **Certifications** | Fabric hereda certificaciones de cumplimiento de Microsoft 365 y Azure (SOC 1/2, ISO 27001, HIPAA, GDPR, etc.) |

> **Nota:** Para industrias altamente reguladas, verifica que las certificaciones de cumplimiento específicas requeridas por tu organización estén admitidas por Fabric en tu región.

**Referencia:** [Governance and compliance in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/governance/governance-compliance-overview)

---

## 4. Roles y permisos de Workspace

### 4.1 Resumen de roles de Workspace

Los workspaces de Fabric usan un modelo de control de acceso basado en roles (RBAC) con cuatro roles integrados.

#### Los cuatro roles de Workspace

| Rol | Descripción |
|---|---|
| **Admin** | Control total sobre el workspace, incluida la administración de membresía, configuración y todos los `items` |
| **Member** | Puede crear, editar y eliminar todos los `items` en el workspace. No puede administrar la configuración del workspace ni la membresía |
| **Contributor** | Puede crear, editar y eliminar `items` en el workspace. No puede publicar ni administrar `apps`, ni administrar membresía |
| **Viewer** | Puede ver e interactuar con todos los `items`, pero no puede crear, editar ni eliminar nada |

### 4.2 Matriz de permisos

La siguiente tabla detalla los permisos para cada rol:

| Permiso | Admin | Member | Contributor | Viewer |
|---|---|---|---|---|
| **Ver items del workspace** | ✅ | ✅ | ✅ | ✅ |
| **Leer datos** | ✅ | ✅ | ✅ | ✅ |
| **Conectarse a SQL analytics endpoint** | ✅ | ✅ | ✅ | ✅ |
| **Crear items** | ✅ | ✅ | ✅ | ❌ |
| **Editar items** | ✅ | ✅ | ✅ | ❌ |
| **Eliminar items** | ✅ | ✅ | ✅ | ❌ |
| **Ejecutar notebooks y trabajos de Spark** | ✅ | ✅ | ✅ | ❌ |
| **Ejecutar pipelines** | ✅ | ✅ | ✅ | ❌ |
| **Crear y publicar apps** | ✅ | ✅ | ❌ | ❌ |
| **Compartir items con otros** | ✅ | ✅ | ❌ | ❌ |
| **Agregar Members/Contributors/Viewers** | ✅ | ✅ | ❌ | ❌ |
| **Actualizar metadatos del workspace** | ✅ | ❌ | ❌ | ❌ |
| **Agregar/Quitar Admins** | ✅ | ❌ | ❌ | ❌ |
| **Administrar configuración del workspace** | ✅ | ❌ | ❌ | ❌ |
| **Configurar integración con Git** | ✅ | ❌ | ❌ | ❌ |
| **Eliminar workspace** | ✅ | ❌ | ❌ | ❌ |
| **Asignar workspace a capacity** | ✅ | ❌ | ❌ | ❌ |
| **Administrar configuración de Spark a nivel de workspace** | ✅ | ❌ | ❌ | ❌ |

### 4.3 Buenas prácticas para la asignación de roles

1. **Principio de privilegio mínimo:** Asigna el rol mínimo necesario para cada usuario o grupo
2. **Usa grupos de seguridad:** Asigna roles a grupos de seguridad de Entra ID (Azure AD) en lugar de usuarios individuales
3. **Limita el acceso Admin:** Solo los propietarios del workspace y los administradores de TI deben tener el rol Admin
4. **Viewer para consumidores:** A los consumidores de informes se les debe asignar el rol Viewer
5. **Contributor para desarrolladores:** Los data engineers y desarrolladores que crean `items` pero no administran el workspace deben ser Contributors
6. **Member para líderes:** Los líderes de equipo que necesitan compartir y publicar contenido deben ser Members

> **Consejo de examen:** Una pregunta común del examen consiste en determinar el rol mínimo necesario para una tarea específica. Recuerda:  
> - Compartir items → **Member** (mínimo)  
> - Crear items → **Contributor** (mínimo)  
> - Administrar workspace → **Admin** únicamente

### 4.4 Acceso con Service Principal

Los `service principals` (registros de aplicaciones en Entra ID) pueden usarse para acceso automatizado y programático a workspaces de Fabric.

#### Habilitar acceso con Service Principal

1. **Configuración del tenant:** El administrador de Fabric debe habilitar `"Service principals can use Fabric APIs"` en la configuración de administración del tenant
2. **Rol de workspace:** Agrega el `service principal` (o su grupo de seguridad) al workspace con el rol apropiado
3. **Acceso a API:** El `service principal` se autentica mediante el flujo de credenciales de cliente OAuth 2.0

#### Escenarios comunes de Service Principal

| Escenario | Descripción |
|---|---|
| **Implementaciones automatizadas** | Pipelines de CI/CD que despliegan `items` de Fabric |
| **Actualización de datos programada** | Disparar ejecuciones de pipelines de datos de forma programática |
| **Embedding** | Insertar reports de Fabric en aplicaciones personalizadas |
| **Operaciones de administración** | Administración a nivel de tenant mediante REST APIs |

```python
# Ejemplo: Autenticación con un service principal
import msal

app = msal.ConfidentialClientApplication(
    client_id="<your-client-id>",
    client_credential="<your-client-secret>",
    authority="https://login.microsoftonline.com/<tenant-id>"
)

result = app.acquire_token_for_client(
    scopes=["https://api.fabric.microsoft.com/.default"]
)

access_token = result["access_token"]
```

> **Buena práctica:** Usa identidades administradas o autenticación basada en certificados para `service principals` en producción, en lugar de `client secrets`. Rota los secretos periódicamente si es necesario usarlos.

### 4.5 Permisos a nivel de item

Además de los roles de workspace, Fabric admite permisos granulares a nivel de item:

#### Uso compartido y permisos por item

| Permiso | Descripción |
|---|---|
| **Read** | Ver el item y sus datos |
| **ReadAll** | Leer todos los datos mediante SQL analytics endpoint o XMLA endpoint |
| **Write** | Editar el item |
| **Reshare** | Compartir el item con otros |
| **Execute** | Ejecutar el item (para `items` ejecutables como notebooks) |
| **Explore** | Acceder al `Power BI semantic model` para crear informes |

#### OneLake Data Access Roles (Preview)

Los `OneLake data access roles` proporcionan control de acceso detallado a nivel de carpeta dentro de un `lakehouse`:

- Definen roles personalizados con acceso de lectura a carpetas/tablas específicas
- Se aplican además de los roles de workspace
- Permiten conceptos de seguridad a nivel de columna y fila en la capa de almacenamiento

**Referencia:** [Workspace roles in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/get-started/roles-workspaces)

---

## 5. Monitoreo y auditoría del uso

### 5.1 Monitoring Hub

El **Monitoring Hub** es el lugar centralizado para rastrear el estado de las actividades en tu workspace de Fabric.

#### Qué muestra el Monitoring Hub

| Información | Descripción |
|---|---|
| **Activity name** | Nombre del notebook, pipeline, trabajo de Spark, etc. |
| **Activity type** | Tipo de item que se está ejecutando |
| **Status** | En progreso, completado, fallido, cancelado |
| **Start time** | Cuándo comenzó la actividad |
| **Duration** | Cuánto tiempo lleva ejecutándose la actividad o tiempo total de ejecución |
| **Submitted by** | Quién disparó la actividad |
| **Location** | El workspace donde se está ejecutando la actividad |

#### Acceder al Monitoring Hub

- Haz clic en **Monitoring Hub** en el panel de navegación izquierdo del portal de Fabric
- Filtra por tipo de item, estado, rango de tiempo y workspace
- Haz `drill into` sobre actividades individuales para obtener información detallada de ejecución

> **Nota:** El Monitoring Hub muestra actividades de los workspaces a los que tienes acceso. NO muestra actividades de todo el tenant; para eso se requiere el `Admin monitoring workspace`.

### 5.2 Admin Monitoring Workspace

El **Admin monitoring workspace** es un workspace especial generado por el sistema y disponible para administradores de Fabric.

#### Funcionalidades

- Informes preconstruidos sobre el uso de Fabric a nivel de tenant
- Proporciona un `semantic model` con datos detallados de actividad
- Incluye datos sobre:
  - Inventario de `items` en todos los workspaces
  - Actividades de usuario y patrones de acceso
  - Estadísticas de uso de funcionalidades
  - Utilización de capacity

#### Requisitos de acceso

- Debes ser **Fabric administrator** o **Global administrator** para acceder
- Los usuarios no pueden modificarlo ni eliminarlo
- Se crea y mantiene automáticamente por el sistema

### 5.3 Capacity Metrics App

La **Microsoft Fabric Capacity Metrics App** es una `Power BI app` dedicada al monitoreo del consumo de capacity.

#### Instalación y configuración

1. Instálala desde AppSource (busca `"Microsoft Fabric Capacity Metrics"`)
2. Conéctala a tu capacity de Fabric
3. Configura actualización programada para monitoreo continuo

#### Métricas clave disponibles

| Métrica | Descripción |
|---|---|
| **CU consumption** | Uso total de Capacity Units a lo largo del tiempo |
| **CU % utilization** | Porcentaje de CUs disponibles que se están usando |
| **Throttling** | Instancias donde las operaciones se retrasaron por sobreutilización |
| **Interactive vs. Background** | Desglose del uso de `compute` por tipo de operación |
| **By item type** | Consumo de CU desglosado por item (`Lakehouse`, `Warehouse`, `Notebook`, etc.) |
| **By workspace** | Consumo de CU por workspace |
| **By user** | Quién consume más capacity |
| **Overages** | Períodos donde el uso excedió la asignación de capacity |

#### Páginas clave en Capacity Metrics App

1. **Multi-metric ribbon chart:** Visualiza CU%, `throttling` y `overages` en una ventana de 14 días
2. **Timepoint detail table:** Permite profundizar en ventanas de tiempo específicas para ver qué `items` consumen capacity
3. **Throttling detail:** Ayuda a entender cuándo y por qué ocurrió el `throttling`

> **Consejo de examen:** La Capacity Metrics app usa una **agregación de 30 segundos** para rastrear el consumo de CU. Las operaciones en background (p. ej., actualización de datos) se suavizan en una **ventana de 24 horas**, mientras que las operaciones interactivas se evalúan en ventanas más cortas.

### 5.4 Audit Logs y eventos de actividad

Las actividades de Fabric se rastrean en el **Microsoft 365 unified audit log**, proporcionando un registro completo de las acciones de usuarios y del sistema.

#### Habilitar audit logging

- El `audit logging` está habilitado de forma predeterminada para tenants de Microsoft 365
- Es posible que la configuración de administración de Fabric deba ajustarse para garantizar que se capturen todas las actividades
- Los logs se conservan durante 90 días (predeterminado) o hasta 10 años (con licencias avanzadas de auditoría)

#### Acceder a los audit logs

| Método | Descripción |
|---|---|
| **Microsoft Purview compliance portal** | Buscar y filtrar `audit logs` a través de la UI |
| **PowerShell** | Usar el cmdlet `Search-UnifiedAuditLog` |
| **REST API** | Office 365 Management Activity API |
| **Microsoft Sentinel** | Enviar `audit logs` a Sentinel para integración SIEM |

#### Actividades auditadas comunes

```
# Ejemplos de actividades de Fabric auditadas:
- CreateWorkspace
- DeleteWorkspace
- AddWorkspaceMember
- RemoveWorkspaceMember
- CreateReport
- ViewReport
- ExportReport
- CreateLakehouse
- RunNotebook
- ExecutePipeline
- ShareItem
- ChangeSensitivityLabel
- UpdateCapacitySetting
```

> **Buena práctica:** Configura alertas automáticas para actividades críticas como eliminación de workspaces, cambios de permisos y exportación de datos. Usa Microsoft Sentinel o una solución SIEM para monitoreo en tiempo real.

#### Ejemplo de PowerShell — Consulta de audit logs

```powershell
# Buscar todas las actividades de Fabric en los últimos 7 días
Search-UnifiedAuditLog `
    -StartDate (Get-Date).AddDays(-7) `
    -EndDate (Get-Date) `
    -RecordType PowerBIAudit `
    -ResultSize 1000
```

### 5.5 Informes de métricas de uso

Las métricas de uso proporcionan información a nivel de workspace sobre cómo se están usando los `reports` y `dashboards`.

#### Métricas disponibles

| Métrica | Descripción |
|---|---|
| **Report views** | Número de veces que se visualizó cada report |
| **Unique viewers** | Número de usuarios distintos que visualizaron reports |
| **View trend** | Cómo cambian con el tiempo las visualizaciones de reports |
| **Distribution method** | Cómo accedieron los usuarios al report (directo, app, compartido) |
| **Platform** | Desglose por dispositivo/navegador |
| **Top users** | Consumidores más activos del contenido del workspace |

> **Nota:** Las métricas de uso están disponibles para Members y Admins del workspace. Se puede generar un informe preconstruido `"Usage metrics report"` desde el menú contextual de cualquier report.

**Referencia:** [Monitor Fabric usage](https://learn.microsoft.com/en-us/fabric/admin/monitoring-hub)

---

## 6. Data lineage y análisis de impacto

### 6.1 Lineage view en Fabric

La **Lineage view** proporciona una representación visual de cómo fluyen los datos a través de los `items` dentro de un workspace.

#### Acceder a Lineage view

1. Abre un workspace en el portal de Fabric
2. Haz clic en **Lineage view** (ícono en la barra superior derecha, junto a la vista de lista y la vista de tarjetas)
3. La visualización muestra todos los `items` y sus dependencias

#### Qué muestra Lineage view

```
[Fuente de datos] → [Dataflow Gen2] → [Lakehouse] → [Semantic Model] → [Report]
     │                                  │
     └──────→ [Warehouse] ─────────────┘
```

- **Dependencias upstream:** De dónde provienen los datos
- **Dependencias downstream:** Qué `items` dependen de estos datos
- **Fuentes de datos externas:** Conexiones a orígenes fuera de Fabric
- **Dirección del flujo de datos:** Flechas visuales que muestran el flujo de datos

#### Funcionalidades clave

| Funcionalidad | Descripción |
|---|---|
| **Visual dependency graph** | Diagrama interactivo que muestra relaciones entre `items` |
| **External source tracking** | Muestra conexiones a fuentes de datos externas (SQL Server, APIs, archivos, etc.) |
| **Cross-workspace lineage** | Muestra dependencias entre workspaces cuando los `items` se referencian entre sí |
| **Drill-down** | Haz clic en cualquier item para ver sus dependencias upstream y downstream específicas |

### 6.2 Impact analysis

El **Impact analysis** te ayuda a comprender los efectos downstream de cambiar o eliminar un item.

#### Cómo funciona Impact analysis

1. Haz clic derecho en cualquier item del workspace (o usa el menú del item)
2. Selecciona **Impact analysis**
3. Consulta un resumen que muestra:
   - Número de workspaces dependientes
   - Número de `items` dependientes
   - Número aproximado de usuarios afectados (según datos de visualización de reports)

#### Ejemplo de resumen de Impact analysis

```
Cambio: Sales_Lakehouse

Resumen de impacto:
├── 3 Workspaces dependientes
├── 12 Items dependientes
│   ├── 2 Semantic models
│   ├── 5 Reports
│   ├── 3 Dashboards
│   └── 2 Dataflows
├── ~45 viewers afectados (últimos 30 días)
└── Contacto: data-engineering@contoso.com
```

> **Consejo de examen:** El `impact analysis` usa **datos de uso** para estimar usuarios afectados. Si un report fue visto por 45 personas en los últimos 30 días, ese es el impacto estimado. Ejecuta siempre `impact analysis` ANTES de realizar cambios disruptivos.

### 6.3 Capacidades de Data Catalog

Fabric proporciona funcionalidades de descubrimiento y catálogo de datos mediante la integración con OneLake y el `data hub`.

#### Data Hub

El **Data Hub** (accesible desde la navegación izquierda) permite a los usuarios:

- Explorar `data items` disponibles en los workspaces a los que tienen acceso
- Buscar tablas, `lakehouses`, `warehouses` y `semantic models` específicos
- Filtrar por estado de `endorsement`, `sensitivity label` y tipo de item
- Ver detalles del item, incluidos descripción, propietario y hora de la última actualización

#### Funcionalidades de OneLake Data Hub

| Funcionalidad | Descripción |
|---|---|
| **Search** | Búsqueda de texto completo en nombres y descripciones de `items` |
| **Filter** | Filtrar por tipo, `endorsement`, workspace y más |
| **Preview** | Vista previa de datos de tablas directamente desde el hub |
| **Details** | Ver metadatos, esquema, descripción y estado de `endorsement` |
| **Quick actions** | Abrir `items`, crear reports o copiar cadenas de conexión SQL |

### 6.4 Endorsement (Promoted y Certified)

`Endorsement` es un mecanismo para ayudar a los usuarios a identificar activos de datos confiables y de alta calidad.

#### Niveles de Endorsement

| Nivel | Descripción | Quién puede aplicarlo |
|---|---|---|
| **None** | Estado predeterminado — sin endorsement | — |
| **Promoted** | El item se considera listo para producción y valioso | Cualquier Member o Admin del workspace |
| **Certified** | El item ha sido revisado y cumple los estándares de calidad de la organización | Solo usuarios autorizados por el administrador de Fabric (configurado en la configuración del tenant) |

#### Cómo aplicar Endorsement a un item

1. Abre la configuración del item
2. Ve a la sección **Endorsement**
3. Selecciona **Promoted** o **Certified** (si estás autorizado)
4. Opcionalmente, agrega una descripción que explique el endorsement

#### Buenas prácticas de Endorsement

- **Promoted:** Úsalo para `items` que están listos para un consumo más amplio, pero que aún no pasaron por una revisión formal
- **Certified:** Resérvalo para `items` que fueron validados, documentados y aprobados por un `data steward` o equipo de gobernanza
- Define un proceso de certificación claro en tu organización
- Limita quién puede certificar `items` (configurado en la administración de Fabric bajo Endorsement)

> **Buena práctica:** Anima a los usuarios a buscar primero `items` **Certified** cuando necesiten datos. Esto ayuda a evitar que construyan reports sobre datos no validados o de prueba.

#### Indicadores visuales

Los `items` con `endorsement` muestran insignias en todo el portal de Fabric:

- 🏷️ **Promoted:** Insignia azul con un ícono de flecha hacia arriba
- ✅ **Certified:** Insignia naranja con una marca de verificación

Estas insignias aparecen en:
- Vista de lista del workspace
- Resultados de búsqueda del Data Hub
- Lineage view
- Cuadros de diálogo Get Data en Power BI

**Referencia:** [Endorsement in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/governance/endorsement-overview)

---

## 7. Administrar Capacity y asignación de recursos

### 7.1 Conceptos de Capacity en Fabric

Comprender cómo funciona la capacity de Fabric es esencial para administrar costos y rendimiento.

#### Capacity Units (CUs)

- La unidad fundamental de `compute` en Fabric
- Todas las cargas de trabajo (Spark, SQL, Power BI, pipelines, etc.) consumen CUs
- El consumo de CU se mide por segundo
- Diferentes operaciones consumen distintas cantidades de CUs

#### Cómo funciona el consumo de CU

```
┌─────────────────────────────────────────────────┐
│              Fabric Capacity (F64)              │
│              64 CU disponibles                  │
│                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │ Lakehouse│  │Warehouse │  │ Power BI │       │
│  │  Spark   │  │  T-SQL   │  │ Queries  │       │
│  │  20 CU   │  │  15 CU   │  │  8 CU    │       │
│  └──────────┘  └──────────┘  └──────────┘       │
│                                                 │
│  Total: 43 CU / 64 CU = 67% de utilización      │
└─────────────────────────────────────────────────┘
```

#### Tipos de operación y throttling

Fabric clasifica las operaciones en dos categorías:

| Tipo | Descripción | Ventana de smoothing | Comportamiento de throttling |
|---|---|---|---|
| **Interactive** | Consultas iniciadas por el usuario, visualización de reports, ejecuciones ad hoc de notebooks | 5 minutos | Se retrasan cuando se excede la capacity |
| **Background** | Actualizaciones programadas, trabajos de Spark, ejecuciones de pipelines | 24 horas | Se ponen en cola y se retrasan; pueden rechazarse en sobrecarga extrema |

#### Etapas de throttling

| Etapa | Uso de CU | Efecto |
|---|---|---|
| **Sin throttling** | 0–100% de la asignación de CU | Funcionamiento normal |
| **Smoothing** | Picos temporales por encima del 100% | Las operaciones continúan normalmente; el uso se suaviza con el tiempo |
| **Retraso interactivo** | Sobreuso sostenido (>100%) | Las operaciones interactivas experimentan retrasos crecientes (10 s, 20 s, 40 s, etc.) |
| **Rechazo en background** | Sobreuso extremo y sostenido | Las operaciones en background pueden ser rechazadas; las interactivas se retrasan mucho |

> **Consejo de examen:** Fabric usa **background smoothing** en una ventana de 24 horas. Esto significa que un trabajo grande de Spark que consuma muchas CUs puede repartirse a lo largo del día sin causar `throttling` inmediato. Las operaciones interactivas, en cambio, se evalúan en ventanas más cortas y harán `throttle` antes.

### 7.2 Configuración y administración de Capacity

#### Administración de Capacity

La capacity puede administrarse desde varios lugares:

| Ubicación | Capacidades |
|---|---|
| **Azure portal** | Crear, redimensionar, pausar/reanudar capacities de Fabric |
| **Fabric Admin portal** | Asignar workspaces, administrar configuración del tenant, delegar capacity |
| **Fabric Admin APIs** | Administración programática de capacity |

#### Configuraciones clave de Capacity

| Configuración | Descripción |
|---|---|
| **Region** | Región de Azure donde se implementa la capacity (afecta `data residency` y latencia) |
| **SKU / Size** | La asignación de CU (F2 hasta F2048) |
| **Capacity admins** | Usuarios que pueden administrar la capacity |
| **Workspace assignment** | Qué workspaces usan esta capacity |
| **Notifications** | Umbrales de alerta para utilización de capacity |
| **Autoscale** | Configuración de escalado automático |

#### Pausar y reanudar Capacity

Una funcionalidad importante para optimizar costos es la capacidad de **pausar** una capacity de Fabric:

- Cuando está en pausa, no se generan cargos por CU
- Los datos en OneLake se conservan (los cargos por almacenamiento siguen aplicando)
- Las operaciones en ejecución fallarán cuando la capacity esté pausada
- Útil para capacities de desarrollo/pruebas que solo se necesitan en horario laboral

```bash
# Ejemplo de Azure CLI: Pausar una capacity de Fabric
az fabric capacity suspend \
    --resource-group "rg-fabric" \
    --capacity-name "fabriccapacity01"

# Reanudar una capacity de Fabric
az fabric capacity resume \
    --resource-group "rg-fabric" \
    --capacity-name "fabriccapacity01"
```

### 7.3 Configuración de Autoscale

Autoscale permite a Fabric agregar automáticamente CUs cuando la demanda supera la capacity base.

#### Cómo funciona Autoscale

1. Configuras un número máximo de CUs adicionales
2. Cuando la utilización supera el 100% de la capacity base, Fabric agrega CUs en incrementos
3. Las CUs agregadas por Autoscale se facturan por separado a una tarifa por CU y por segundo
4. Cuando la demanda disminuye, las CUs adicionales se liberan

#### Configuración

| Parámetro | Descripción |
|---|---|
| **Enable autoscale** | Activar/desactivar |
| **Max CU add-on** | Número máximo de CUs que pueden agregarse automáticamente |
| **Notification threshold** | Porcentaje de CU a partir del cual se notifica a los administradores |

> **Nota:** Autoscale está diseñado para manejar picos temporales, no crecimiento sostenido. Si necesitas más CUs de forma constante, considera ampliar tu SKU base.

### 7.4 Estrategias de optimización de costos

#### Estrategia 1: Ajusta el tamaño correcto de tu Capacity

- Empieza con un SKU más pequeño y monitorea el uso con Capacity Metrics app
- Escala solo cuando observes `throttling` consistente o problemas de rendimiento
- Considera capacities separadas para Dev/Test (más pequeñas, se pueden pausar) y Producción (más grandes, siempre activas)

#### Estrategia 2: Programar pausa/reanudación de Capacity

Para entornos no productivos:

```python
# Ejemplo: Runbook de Azure Automation para pausar la capacity por la noche
# Programación: Todos los días a las 7:00 PM

# Pausar a las 7 PM
az fabric capacity suspend --resource-group "rg-fabric" --capacity-name "dev-capacity"

# Reanudar a las 7 AM (runbook separado)
az fabric capacity resume --resource-group "rg-fabric" --capacity-name "dev-capacity"
```

#### Estrategia 3: Optimizar cargas de trabajo

| Técnica | Beneficio |
|---|---|
| **V-Order optimization** | Lecturas más rápidas con archivos Parquet optimizados (habilitado por defecto) |
| **Partition pruning** | Consulta solo las particiones relevantes para reducir el consumo de CU |
| **Incremental refresh** | Actualiza solo datos nuevos/cambiados en lugar de una actualización completa |
| **Query folding** | Empuja transformaciones hacia el origen para reducir `compute` en Fabric |
| **Caching** | Usa modo import de Power BI o agregaciones para reducir la carga de consultas |

#### Estrategia 4: Asignación Workspace-to-Capacity

| Tipo de Workspace | Capacity recomendada |
|---|---|
| Development/Sandbox | Compartida (SKU más pequeño, se puede pausar) |
| Testing/QA | Compartida (SKU más pequeño, se puede pausar) |
| Production Analytics | Dedicada (tamaño adecuado, siempre activa) |
| Ad hoc / Exploration | Trial o capacity compartida |

#### Estrategia 5: Monitorear y gobernar

- **Configura alertas** para umbrales de utilización de capacity (p. ej., 80% de uso sostenido)
- **Revisa** Capacity Metrics app semanalmente
- **Implementa chargeback** — usa seguimiento de CU a nivel de workspace para asignar costos a unidades de negocio
- **Audita items no utilizados** — identifica y elimina `lakehouses`, `warehouses` y `reports` sin uso para liberar capacity

> **Consejo de examen:** Las preguntas de optimización de costos en el examen suelen centrarse en elegir el SKU correcto, pausar capacity no productiva y optimizar patrones de carga de trabajo. Conoce la diferencia entre las ventanas de smoothing para operaciones interactivas y de background.

**Referencia:** [Microsoft Fabric capacity management](https://learn.microsoft.com/en-us/fabric/enterprise/licenses)

---

## Revisión rápida — Resumen de conceptos clave

| Concepto | Idea clave |
|---|---|
| **OneLake** | Un único data lake unificado por tenant; todos los `items` de Fabric almacenan datos aquí |
| **Workspace** | Contenedor organizativo para `items` de Fabric; tiene su propio acceso basado en roles |
| **Capacity** | Pool de `compute` medido en CUs; determina rendimiento y costo |
| **Lakehouse** | `Schema-on-read`, Spark + SQL analytics endpoint (T-SQL de solo lectura) |
| **Warehouse** | `Schema-on-write`, soporte completo para T-SQL DML |
| **Sensitivity labels** | Fluyen downstream a través del `lineage`; aplican protección de datos |
| **Endorsement** | `Promoted` (autoservicio) y `Certified` (controlado por admin) |
| **Monitoring Hub** | Seguimiento de actividad por usuario dentro de workspaces accesibles |
| **Capacity Metrics App** | Análisis detallado de consumo de CU y `throttling` |
| **Git integration** | Control de versiones para definiciones de `items` (NO datos) |
| **DLP policies** | Detectan datos sensibles en `semantic models` y `lakehouses` |
| **Lineage view** | Grafo visual de dependencias de datos dentro de un workspace |
| **Impact analysis** | Evaluación de dependencias downstream e impacto en usuarios |
| **Throttling** | Ocurre cuando el uso de CU excede la capacity; las operaciones en background se suavizan durante 24 h |

---

## Consejos de preparación para el examen del Dominio 1

1. **Comprende los límites de cada rol:** Conoce exactamente qué puede y qué no puede hacer cada rol de workspace (`Admin`, `Member`, `Contributor`, `Viewer`). Espera al menos una pregunta sobre el rol mínimo requerido.

2. **Conoce Lakehouse vs. Warehouse:** El diferenciador clave es el soporte de escritura con T-SQL. El SQL analytics endpoint de Lakehouse es **de solo lectura**.

3. **Capacity y throttling:** Comprende que las operaciones en background se suavizan durante 24 horas y las interactivas en ventanas más cortas. Autoscale maneja picos; el SKU base cubre la línea base.

4. **Las sensitivity labels fluyen downstream:** Si un `lakehouse` está etiquetado como `"Confidential"`, los `semantic models` y `reports` downstream heredan esa label.

5. **La integración con Git rastrea metadatos, no datos:** Las definiciones de `items` se controlan por versión; los datos reales en OneLake no se almacenan en Git.

6. **Los shortcuts de OneLake no copian datos:** Referencian datos externos en el lugar. Esto es crítico para escenarios de `data mesh` y entre workspaces.

7. **Los service principals requieren habilitación del administrador del tenant:** El administrador del tenant de Fabric debe permitir explícitamente que los `service principals` usen Fabric APIs.

8. **Impact analysis muestra impacto en usuarios:** Usa datos históricos de uso (últimos 30 días) para estimar cuántos usuarios se verían afectados por un cambio.

---

## Recursos adicionales

- [Documentación de Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/)
- [Capacity y licencias de Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/enterprise/licenses)
- [Workspace roles in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/get-started/roles-workspaces)
- [Git integration in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/intro-to-git-integration)
- [Governance and compliance in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/governance/governance-compliance-overview)
- [Endorsement in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/governance/endorsement-overview)
- [Microsoft Fabric Monitoring Hub](https://learn.microsoft.com/en-us/fabric/admin/monitoring-hub)
- [OneLake overview](https://learn.microsoft.com/en-us/fabric/onelake/onelake-overview)
- [Data Loss Prevention in Fabric](https://learn.microsoft.com/en-us/fabric/governance/data-loss-prevention-overview)

---

*Última actualización: 2025*
