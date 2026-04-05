# Dominio 1: Preguntas de práctica — Planear, implementar y administrar una solución de analítica de datos

> **50 preguntas de opción múltiple** que cubren el Dominio 1 del examen DP-600 (ponderación del 10–15%).
> Cada pregunta incluye una explicación detallada de la respuesta.

---

### Pregunta 1

Tu organización está adoptando Microsoft Fabric. El equipo de data engineering necesita una capa de almacenamiento única y unificada a la que todas las cargas de trabajo de Fabric puedan acceder sin copiar datos entre servicios. ¿Qué componente de Microsoft Fabric ofrece esta capacidad?

- A) Azure Data Lake Storage Gen2
- B) OneLake
- C) Power BI Premium capacity
- D) Azure Synapse workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) OneLake**

**Explicación:** OneLake es el data lake unificado integrado en Microsoft Fabric que actúa como la única capa de almacenamiento para todas las cargas de trabajo de Fabric. Elimina los silos de datos al proporcionar una sola copia de los datos accesible desde las experiencias de Data Engineering, Data Warehouse, Data Science y Power BI. OneLake está construido sobre tecnología ADLS Gen2, pero se administra como parte de Fabric.

**Por qué las otras opciones son incorrectas:**
- A) ADLS Gen2 es un servicio independiente de Azure; aunque OneLake usa una tecnología similar, ADLS Gen2 por sí mismo no es la capa unificada integrada con Fabric.
- C) Power BI Premium capacity es un nivel de licenciamiento y `compute`, no una capa de almacenamiento.
- D) Azure Synapse workspace es un servicio de analítica independiente anterior al enfoque unificado de Fabric.

</details>

---

### Pregunta 2

Un responsable de cumplimiento te pide garantizar que los informes financieros confidenciales en un workspace de Fabric lleven automáticamente labels de protección cuando se compartan externamente. ¿Qué funcionalidad debes implementar?

- A) Row-level security
- B) Microsoft Purview sensitivity labels
- C) Workspace roles
- D) Microsoft Entra ID Conditional Access

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Microsoft Purview sensitivity labels**

**Explicación:** Las Microsoft Purview sensitivity labels clasifican y protegen los datos según su nivel de sensibilidad. Cuando se aplican a `items` de Fabric, estas labels persisten cuando el contenido se comparte, exporta o mueve, asegurando que la información confidencial conserve su clasificación de protección. Las sensitivity labels se integran con las políticas de Microsoft Information Protection para su aplicación downstream.

**Por qué las otras opciones son incorrectas:**
- A) Row-level security controla qué filas puede ver un usuario dentro de un `dataset`, no la clasificación o protección del contenido compartido.
- C) Los Workspace roles controlan el acceso dentro de un workspace, pero no aplican labels de protección persistentes al contenido.
- D) Las políticas de Conditional Access controlan la autenticación y las condiciones de acceso, pero no clasifican ni etiquetan el contenido.

</details>

---

### Pregunta 3

Administras un tenant de Fabric con varios departamentos. Cada departamento debe poder gestionar sus propios `items` de workspace de forma independiente, pero un equipo central de TI debe conservar la capacidad de gobernar el uso de capacity en toda la organización. ¿Cuál es el enfoque recomendado?

- A) Crear un solo workspace y asignar carpetas por departamento
- B) Crear workspaces separados por departamento y asignarlos a capacities administradas centralmente
- C) Dar a cada departamento su propio tenant de Fabric
- D) Usar una sola licencia Power BI Pro para toda la organización

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Crear workspaces separados por departamento y asignarlos a capacities administradas centralmente**

**Explicación:** Los workspaces separados proporcionan autonomía a cada departamento para administrar sus `items`, mientras que asignar esos workspaces a capacities administradas centralmente permite a TI controlar y supervisar el uso de recursos en toda la organización. Este modelo equilibra la analítica de autoservicio con la gobernanza centralizada.

**Por qué las otras opciones son incorrectas:**
- A) Un único workspace no proporciona independencia departamental y genera desafíos de administración de permisos a escala.
- C) Los tenants separados introducen complejidad innecesaria, impiden la colaboración entre departamentos y aumentan la carga administrativa.
- D) Una licencia Power BI Pro no proporciona funcionalidades de Fabric capacity y es una licencia por usuario, no un modelo de gobernanza organizacional.

</details>

---

### Pregunta 4

Un data engineer quiere conectar una cuenta externa de Azure Data Lake Storage Gen2 a OneLake para que los datos aparezcan como si estuvieran almacenados dentro de Fabric sin copiarlos físicamente. ¿Qué funcionalidad de OneLake permite esto?

- A) Data pipeline
- B) Shortcut
- C) Dataflow Gen2
- D) Mirrored database

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Shortcut**

**Explicación:** Los OneLake shortcuts permiten crear referencias a datos almacenados en ubicaciones externas, como ADLS Gen2, Amazon S3 u otras ubicaciones de OneLake. Los datos permanecen en su ubicación original, pero el shortcut hace que aparezcan como una carpeta dentro de OneLake, permitiendo que todas las cargas de trabajo de Fabric accedan a ellos sin duplicación de datos.

**Por qué las otras opciones son incorrectas:**
- A) Un data pipeline copia o transforma datos entre orígenes y destinos; no crea referencias virtuales.
- C) Dataflow Gen2 es una herramienta ETL para transformación de datos, no un mecanismo de referencia virtual.
- D) Las mirrored databases replican datos operacionales de bases de datos hacia OneLake, lo que implica movimiento de datos en lugar de referencias virtuales a ADLS Gen2.

</details>

---

### Pregunta 5

Tu empresa exige que todos los cambios en semantic models y reports queden registrados en control de versiones antes de promoverse a producción. ¿Qué funcionalidad de Fabric admite este requisito?

- A) Deployment pipelines
- B) Git integration
- C) Workspace monitoring
- D) Data activator

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Git integration**

**Explicación:** La Git integration de Fabric conecta un workspace con un repositorio Git (como Azure DevOps o GitHub), habilitando el control de versiones de los `items` del workspace. Los cambios se confirman en una `branch`, se revisan y luego se sincronizan con otros entornos, proporcionando trazabilidad completa en control de código fuente antes del despliegue a producción.

**Por qué las otras opciones son incorrectas:**
- A) Deployment pipelines promueven contenido entre etapas, pero no proporcionan por sí solas trazabilidad en control de versiones ni capacidades de `branching`.
- C) Workspace monitoring rastrea métricas de uso y rendimiento, no cambios en el código fuente.
- D) Data Activator desencadena acciones según condiciones de datos y no está relacionado con control de versiones.

</details>

---

### Pregunta 6

Necesitas promover un conjunto de reports y datasets desde un workspace de Development, pasando por Test, hasta llegar a un workspace de Production. ¿Qué funcionalidad de Fabric proporciona un flujo administrado de promoción por múltiples etapas?

- A) Git integration
- B) Deployment pipelines
- C) Power Automate flows
- D) Azure DevOps release pipelines

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Deployment pipelines**

**Explicación:** Los Fabric deployment pipelines proporcionan un mecanismo integrado para promover contenido a través de las etapas de Development, Test y Production. Admiten comparación entre etapas, reglas de despliegue para cambios de parámetros y despliegue selectivo de `items`, lo que los convierte en la herramienta nativa para flujos administrados de promoción dentro de Fabric.

**Por qué las otras opciones son incorrectas:**
- A) Git integration proporciona control de versiones, pero no administra de forma nativa la promoción por múltiples etapas dentro de los workspaces de Fabric.
- C) Power Automate flows puede automatizar tareas, pero no es la herramienta diseñada específicamente para promoción de contenido en Fabric.
- D) Azure DevOps release pipelines son herramientas externas de CI/CD que requieren configuración adicional y no están integradas de forma nativa con el modelo de promoción de Fabric.

</details>

---

### Pregunta 7

Un administrador de workspace asigna a un usuario el rol Viewer en un workspace de Fabric. ¿Qué acción puede realizar ese usuario?

- A) Crear nuevos reports en el workspace
- B) Editar semantic models existentes
- C) Ver e interactuar con reports y dashboards publicados
- D) Configurar las opciones del workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Ver e interactuar con reports y dashboards publicados**

**Explicación:** El rol Viewer es el rol de workspace más restrictivo en Fabric. Los usuarios con este rol pueden ver e interactuar con reports y dashboards que ya se publicaron en el workspace, pero no pueden crear, editar ni eliminar `items` del workspace ni cambiar configuraciones.

**Por qué las otras opciones son incorrectas:**
- A) Crear reports requiere al menos el rol Contributor.
- B) Editar semantic models requiere al menos el rol Contributor.
- D) Configurar las opciones del workspace requiere el rol Admin.

</details>

---

### Pregunta 8

El administrador de Fabric de tu organización necesita supervisar quién accedió a datos sensibles en un lakehouse durante los últimos 30 días. ¿Qué herramienta debería usar?

- A) Capacity Metrics app
- B) Microsoft Purview audit logs
- C) Power BI Desktop Performance Analyzer
- D) SQL Profiler

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Microsoft Purview audit logs**

**Explicación:** Los Microsoft Purview audit logs capturan actividades de usuarios y administradores en Microsoft Fabric, incluidos eventos de acceso a datos. Los administradores pueden buscar en estos logs para identificar quién accedió a `items` específicos, cuándo ocurrió el acceso y qué operaciones se realizaron, por lo que es la herramienta adecuada para auditoría de acceso a lo largo del tiempo.

**Por qué las otras opciones son incorrectas:**
- A) La Capacity Metrics app supervisa utilización de capacity y rendimiento, no eventos de acceso individual a datos por usuario.
- C) Power BI Desktop Performance Analyzer mide el renderizado de reports y el rendimiento de consultas a nivel local, no acceso a datos a nivel de tenant.
- D) SQL Profiler es una herramienta de SQL Server para rastrear eventos del motor de base de datos y no está diseñada para auditoría de acceso a nivel de Fabric.

</details>

---

### Pregunta 9

Estás configurando un workspace de Fabric y necesitas que un usuario pueda compartir `items` y administrar permisos de `items` individuales, pero sin administrar la configuración del workspace. ¿Qué rol de workspace deberías asignarle?

- A) Admin
- B) Member
- C) Contributor
- D) Viewer

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Member**

**Explicación:** El rol Member en un workspace de Fabric permite a los usuarios crear, editar y eliminar `items` del workspace, así como compartir `items` y administrar permisos en `items` individuales. Los Members no pueden cambiar la configuración a nivel de workspace, agregar o quitar otros miembros, ni modificar la asignación de capacity del workspace; esas acciones requieren el rol Admin.

**Por qué las otras opciones son incorrectas:**
- A) Admin puede administrar la configuración del workspace, lo que excede los requisitos indicados.
- C) Contributor puede crear y editar `items`, pero no puede compartir `items` ni administrar permisos a nivel de item.
- D) Viewer solo puede ver contenido y no puede compartir ni administrar permisos.

</details>

---

### Pregunta 10

Una organización quiere rastrear cómo fluyen los datos desde los sistemas origen, pasando por capas de transformación, hasta llegar a los reports finales. ¿Qué capacidad de Microsoft proporciona visibilidad de extremo a extremo sobre este flujo?

- A) Data lineage en Microsoft Fabric
- B) Power BI Q&A
- C) Azure Monitor
- D) Exportación del activity log

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Data lineage en Microsoft Fabric**

**Explicación:** El Data lineage en Fabric proporciona una representación visual de cómo los datos se mueven y transforman desde el origen hasta el destino a través de los `items` del workspace. Muestra orígenes upstream y dependencias downstream, lo que permite realizar `impact analysis` y ayuda a las organizaciones a comprender todo el recorrido de los datos, desde la ingesta hasta el reporting.

**Por qué las otras opciones son incorrectas:**
- B) Power BI Q&A es una funcionalidad de consultas en lenguaje natural para usuarios finales, no una herramienta de visualización de `lineage`.
- C) Azure Monitor rastrea métricas y logs de infraestructura, pero no proporciona vistas de `data lineage` específicas de Fabric.
- D) La exportación del activity log proporciona datos de auditoría a nivel de eventos, no una representación visual del flujo de datos y sus dependencias.

</details>

---

### Pregunta 11

Un administrador de Fabric nota que una capacity específica presenta `throttling` de forma constante durante el horario laboral. ¿Qué debería hacer primero para investigarlo?

- A) Actualizar a todos los usuarios a una licencia Power BI Premium Per User
- B) Revisar la Microsoft Fabric Capacity Metrics app
- C) Eliminar workspaces no usados del tenant
- D) Deshabilitar la actualización de datos en background para todos los semantic models

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Revisar la Microsoft Fabric Capacity Metrics app**

**Explicación:** La Capacity Metrics app proporciona información detallada sobre la utilización de la capacity, incluido el consumo de `compute`, los eventos de `throttling` y qué cargas de trabajo están consumiendo recursos. Este es el primer paso para diagnosticar `throttling` porque identifica la causa raíz antes de tomar cualquier acción correctiva.

**Por qué las otras opciones son incorrectas:**
- A) Actualizar licencias no resuelve el `throttling` de capacity y aborda un problema distinto.
- C) Eliminar workspaces sin comprender la causa puede quitar recursos necesarios y no aborda el problema raíz.
- D) Deshabilitar todas las actualizaciones en background es una acción drástica que solo debería considerarse después de confirmar que esas operaciones son la causa.

</details>

---

### Pregunta 12

Tu empresa tiene una capacity Microsoft Fabric F64. Durante las horas pico, las consultas del data warehouse son lentas mientras que los trabajos de notebooks de data engineering consumen la mayor parte de la capacity. ¿Cuál es la acción más apropiada?

- A) Mover las cargas de trabajo de data engineering a una capacity separada
- B) Eliminar los notebooks que consumen más recursos
- C) Cambiar a todos los usuarios a licencias Power BI Pro
- D) Convertir el lakehouse en una base de datos SQL Server

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Mover las cargas de trabajo de data engineering a una capacity separada**

**Explicación:** Aislar las cargas de trabajo en capacities separadas garantiza que los trabajos intensivos de data engineering no dejen sin recursos a las consultas del data warehouse. Este enfoque permite que cada carga de trabajo tenga recursos de `compute` dedicados y habilita escalado independiente, que es la práctica recomendada para administrar cargas de trabajo en competencia dentro de Fabric.

**Por qué las otras opciones son incorrectas:**
- B) Eliminar notebooks es destructivo y no aborda el problema subyacente de contención de recursos.
- C) Las licencias Power BI Pro no proporcionan funcionalidades de Fabric capacity y, de hecho, reducirían capacidades.
- D) Convertir un lakehouse a SQL Server no es una operación de Fabric y no resuelve la contención de capacity.

</details>

---

### Pregunta 13

Un administrador del tenant de Fabric quiere evitar que los usuarios exporten datos de reports a archivos CSV. ¿Dónde debe configurarse esta restricción?

- A) Configuración de roles del workspace
- B) Tenant settings en el Fabric admin portal
- C) Parámetros individuales del report
- D) Permisos de archivos de OneLake

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Tenant settings en el Fabric admin portal**

**Explicación:** Los tenant settings del Fabric admin portal incluyen controles de exportación que permiten a los administradores habilitar o deshabilitar opciones de exportación de datos como CSV, Excel y otros formatos. Estas configuraciones se aplican a nivel de tenant o de grupo de seguridad, proporcionando gobernanza centralizada sobre las capacidades de exportación de datos.

**Por qué las otras opciones son incorrectas:**
- A) Los roles del workspace controlan el acceso a `items` del workspace, no restricciones sobre formatos de exportación.
- C) Los parámetros individuales del report controlan comportamiento y filtrado del report, no permisos de exportación.
- D) Los permisos de archivos de OneLake gobiernan el acceso directo a archivos en el data lake, no las opciones de exportación a nivel de report.

</details>

---

### Pregunta 14

Estás diseñando una solución de Fabric para una empresa multinacional. Los datos de clientes europeos deben permanecer en la región de la UE. ¿Cómo debes garantizar el cumplimiento de `data residency`?

- A) Crear workspaces con nombres prefijados con `"EU"`
- B) Aprovisionar una Fabric capacity en una región de Azure de la UE y asignarle los workspaces de la UE
- C) Aplicar sensitivity labels marcadas como `"EU Only"`
- D) Usar row-level security para filtrar datos de la UE

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Aprovisionar una Fabric capacity en una región de Azure de la UE y asignarle los workspaces de la UE**

**Explicación:** Las capacities de Fabric se aprovisionan en regiones específicas de Azure, y los datos almacenados en workspaces asignados a esa capacity residen en esa región. Al crear una capacity en una región de la UE y asignar todos los workspaces relacionados con la UE a dicha capacity, garantizas que los datos de clientes europeos permanezcan dentro de los límites de la UE para cumplir con los requisitos de `data residency`.

**Por qué las otras opciones son incorrectas:**
- A) Las convenciones de nombres no tienen ningún efecto sobre dónde se almacenan físicamente los datos.
- C) Las sensitivity labels clasifican y protegen datos, pero no controlan la región física de almacenamiento.
- D) Row-level security controla la visibilidad de los datos por usuario, pero no afecta dónde se almacenan físicamente.

</details>

---

### Pregunta 15

Un workspace de Fabric usa actualmente Git integration con Azure DevOps. Un desarrollador hace `commit` de cambios en la `branch` conectada, pero el workspace no refleja las actualizaciones. ¿Cuál es la causa más probable?

- A) El workspace no se ha sincronizado desde el repositorio Git
- B) La licencia Power BI Pro del desarrollador expiró
- C) OneLake está experimentando una interrupción regional
- D) El semantic model es demasiado grande para Git

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) El workspace no se ha sincronizado desde el repositorio Git**

**Explicación:** En la Git integration de Fabric, hacer `commit` de cambios en una `branch` no actualiza automáticamente el workspace. El workspace debe sincronizarse explícitamente para traer los últimos cambios desde el repositorio Git. Hasta que se realice la operación de sincronización, el workspace mantiene su estado anterior.

**Por qué las otras opciones son incorrectas:**
- B) La expiración de una licencia Power BI Pro impediría el acceso al workspace por completo, no provocaría un problema selectivo de sincronización.
- C) Una interrupción de OneLake afectaría operaciones de datos más amplias, no específicamente la sincronización Git-a-workspace.
- D) La Git integration en Fabric administra definiciones de `items` del workspace; los datos del semantic model no se almacenan en Git.

</details>

---

### Pregunta 16

Necesitas asignar un grupo de seguridad como administradores de workspace para 20 workspaces de Fabric. ¿Cuál es el enfoque más eficiente?

- A) Agregar usuarios individuales como administradores a cada workspace manualmente
- B) Asignar al grupo de seguridad el rol Admin en cada workspace
- C) Conceder al grupo de seguridad el rol Fabric Administrator en Microsoft Entra ID
- D) Crear un grupo de Microsoft 365 para cada workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Asignar al grupo de seguridad el rol Admin en cada workspace**

**Explicación:** Asignar un grupo de seguridad al rol Admin en cada workspace permite una administración centralizada de administradores de workspace. Cuando cambia la membresía del grupo, los permisos del workspace se actualizan automáticamente sin requerir modificaciones usuario por usuario en los 20 workspaces.

**Por qué las otras opciones son incorrectas:**
- A) Agregar usuarios individuales a cada workspace consume tiempo y genera una carga de mantenimiento cuando cambia el personal.
- C) El rol Fabric Administrator es un rol a nivel de tenant que proporciona capacidades globales de administración, lo que excede los requisitos de administración a nivel de workspace.
- D) Crear un grupo de Microsoft 365 por workspace es innecesario y no simplifica la administración del mismo conjunto de administradores en varios workspaces.

</details>

---

### Pregunta 17

Un data analyst con el rol Contributor crea un report en un workspace de Fabric. Quiere que un colega fuera del workspace pueda ver el report. ¿Qué debe ocurrir?

- A) El analista puede compartir el report directamente porque los Contributors pueden compartir `items`
- B) Un Member o Admin del workspace debe compartir el report en nombre del Contributor
- C) El colega debe agregarse al workspace como Viewer
- D) B o C

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: D) B o C**

**Explicación:** Los Contributors no pueden compartir `items` ni administrar permisos a nivel de item. Para dar acceso al colega externo, un Member o Admin puede compartir el report directamente, o el colega puede agregarse al workspace con al menos el rol Viewer. Ambas opciones cumplen el requisito.

**Por qué las otras opciones son incorrectas:**
- A) Los Contributors no tienen permisos de uso compartido en un workspace de Fabric.
- B) Esto es parcialmente correcto pero incompleto; agregar al colega como Viewer también es válido.
- C) Esto es parcialmente correcto pero incompleto; que un Member o Admin comparta el report también es válido.

</details>

---

### Pregunta 18

Una organización quiere usar Fabric deployment pipelines. El pipeline tiene tres etapas: Development, Test y Production. ¿Cuál es el rol mínimo de workspace necesario para desplegar contenido en la etapa de Production?

- A) Viewer
- B) Contributor
- C) Member
- D) Admin

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Member**

**Explicación:** Para desplegar contenido a una etapa en un deployment pipeline, el usuario debe ser Member (o Admin) del workspace de destino. Los Members tienen permisos suficientes para crear y actualizar `items` en el workspace, que es justamente lo que realiza la operación de despliegue. Los Contributors no tienen los permisos necesarios en el contexto del pipeline.

**Por qué las otras opciones son incorrectas:**
- A) Los Viewers solo pueden ver contenido y no pueden desplegar.
- B) Los Contributors pueden editar `items`, pero no tienen permisos suficientes para operaciones de despliegue del pipeline.
- D) Admin funciona, pero no es el rol mínimo requerido.

</details>

---

### Pregunta 19

Un administrador de Fabric quiere restringir qué usuarios pueden crear workspaces en el tenant. ¿Dónde se configura esta opción?

- A) Microsoft Entra ID admin center
- B) Tenant settings del Fabric admin portal
- C) Configuración individual de capacity
- D) Configuración de grupos del Microsoft 365 admin center

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Tenant settings del Fabric admin portal**

**Explicación:** El Fabric admin portal incluye una configuración de tenant que controla qué usuarios pueden crear workspaces. Los administradores pueden restringir la creación de workspaces a grupos de seguridad específicos, evitando la proliferación descontrolada de workspaces y manteniendo la gobernanza sobre el entorno de Fabric.

**Por qué las otras opciones son incorrectas:**
- A) Microsoft Entra ID admin center administra identidades y políticas de acceso, pero no contiene controles específicos de Fabric para creación de workspaces.
- C) La configuración de capacity controla asignación de recursos y rendimiento, no permisos para crear workspaces.
- D) El Microsoft 365 admin center administra la creación de grupos de Microsoft 365, lo cual está relacionado, pero no es el control directo para la creación de workspaces en Fabric.

</details>

---

### Pregunta 20

Tu organización aplica una sensitivity label `"Highly Confidential"` a un Power BI semantic model. Un usuario exporta datos desde un report conectado a ese semantic model a un archivo Excel. ¿Qué ocurre con la sensitivity label?

- A) La label se elimina durante la exportación
- B) La label `"Highly Confidential"` se hereda automáticamente en el archivo Excel exportado
- C) Se bloquea por completo la exportación del usuario
- D) El archivo Excel recibe por defecto una label `"General"`

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) La label `"Highly Confidential"` se hereda automáticamente en el archivo Excel exportado**

**Explicación:** Cuando se aplican sensitivity labels al contenido de Power BI, estas fluyen downstream hacia los archivos exportados. Si un usuario exporta datos a Excel, el archivo exportado hereda la sensitivity label del semantic model de origen, garantizando que la protección de datos persista incluso fuera del entorno de Fabric. Esta es una funcionalidad clave de la integración con Microsoft Information Protection.

**Por qué las otras opciones son incorrectas:**
- A) Las labels están diseñadas para persistir a través de las operaciones de exportación, no para eliminarse.
- C) A menos que la política de la label bloquee explícitamente la exportación, esta se permite con la label aplicada.
- D) El archivo exportado hereda la label del origen, no una label predeterminada `"General"`.

</details>

---

### Pregunta 21

Un administrador de Fabric necesita delegar la administración de capacity de una capacity específica a un líder de departamento sin conceder administración completa del tenant. ¿Qué debe hacer el administrador?

- A) Asignar al líder de departamento como Fabric Administrator en Microsoft Entra ID
- B) Agregar al líder de departamento como Capacity Administrator para esa capacity específica
- C) Convertir al líder de departamento en Global Administrator
- D) Darle al líder de departamento el rol Viewer en todos los workspaces de esa capacity

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Agregar al líder de departamento como Capacity Administrator para esa capacity específica**

**Explicación:** Fabric permite asignar Capacity Administrators a nivel de capacity individual. Esto concede al líder de departamento la capacidad de administrar asignaciones de workspace, monitorear rendimiento y configurar opciones para esa capacity específica, sin otorgarle privilegios administrativos en todo el tenant.

**Por qué las otras opciones son incorrectas:**
- A) El rol Fabric Administrator proporciona control en todo el tenant, lo que excede el alcance requerido.
- C) Global Administrator es el rol más amplio de Microsoft Entra ID y supera ampliamente la necesidad de administrar una capacity.
- D) El rol Viewer proporciona acceso de solo lectura al contenido del workspace y no incluye capacidades de administración de capacity.

</details>

---

### Pregunta 22

Estás diseñando una solución de analítica de datos en Fabric. La solución debe admitir consultas T-SQL para analistas de reporting y, al mismo tiempo, permitir trabajos de data engineering basados en Spark. ¿Qué combinación de `items` de Fabric admite mejor ambos requisitos dentro de un solo workspace?

- A) Un lakehouse y un warehouse
- B) Dos datasets de Power BI separados
- C) Una Azure SQL Database y un Spark pool
- D) Una base de datos KQL y un dataflow

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Un lakehouse y un warehouse**

**Explicación:** Un Fabric lakehouse admite tanto ingeniería basada en Spark como un SQL analytics endpoint para consultas T-SQL. Un Fabric warehouse proporciona una experiencia completa de data warehousing con T-SQL. Juntos, estos `items` en el mismo workspace permiten que los data engineers usen notebooks de Spark y que los analistas de reporting usen T-SQL, todo respaldado por OneLake.

**Por qué las otras opciones son incorrectas:**
- B) Los datasets de Power BI son semantic models para reporting y no admiten trabajos de Spark ni data warehousing T-SQL directo.
- C) Azure SQL Database y Spark pools son servicios de Azure separados, no `items` nativos de un workspace de Fabric.
- D) Una base de datos KQL está optimizada para analítica de logs y telemetría usando KQL, no T-SQL estándar, y un dataflow maneja transformación de datos, no consultas SQL interactivas.

</details>

---

### Pregunta 23

Tu equipo usa la Git integration de Fabric. Varios desarrolladores están trabajando simultáneamente sobre los mismos `items` del workspace. ¿Cómo deberían administrar los cambios concurrentes para evitar conflictos?

- A) Cada desarrollador debe trabajar en una `branch` separada y fusionar cambios mediante `pull requests`
- B) Los desarrolladores deben coordinarse verbalmente y turnarse para editar
- C) Bloquear el repositorio Git durante la sesión de cada desarrollador
- D) Deshabilitar la Git integration y usar copia manual de archivos

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Cada desarrollador debe trabajar en una `branch` separada y fusionar cambios mediante `pull requests`**

**Explicación:** El uso de `branches` es la práctica estándar de Git para administrar desarrollo concurrente. Cada desarrollador trabaja en una `branch` aislada, realiza y confirma cambios, y luego fusiona mediante `pull requests`. Esto habilita revisión de código, resolución de conflictos y un historial auditable de todos los cambios.

**Por qué las otras opciones son incorrectas:**
- B) La coordinación verbal no escala y es propensa a errores sin una estrategia sistemática de `branching`.
- C) Bloquear todo el repositorio impide el trabajo concurrente y anula el propósito del control de versiones distribuido.
- D) Deshabilitar la Git integration elimina todos los beneficios de control de versiones y aumenta el riesgo de pérdida de datos y cambios no rastreados.

</details>

---

### Pregunta 24

Un administrador de Fabric observa que un workspace contiene `items` huérfanos: `items` cuyos creadores originales ya no pertenecen a la organización. ¿Cuál es la práctica recomendada para administrar estos `items`?

- A) Eliminar inmediatamente todos los `items` huérfanos
- B) Reasignar la propiedad haciendo que un Admin del workspace tome control de los `items`
- C) Esperar a que los `items` se eliminen automáticamente después de 90 días
- D) Exportar los `items` a un recurso compartido de archivos y archivarlos

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Reasignar la propiedad haciendo que un Admin del workspace tome control de los `items`**

**Explicación:** Cuando los creadores de `items` dejan la organización, los Admins o Members del workspace deben revisar y asumir la propiedad de los `items` huérfanos para garantizar mantenimiento y gobernanza continuos. Esto preserva la continuidad de los datos y asegura que los `items` sigan estando gobernados por las políticas de la organización.

**Por qué las otras opciones son incorrectas:**
- A) Eliminar `items` sin revisión puede quitar reports y activos de datos críticos que aún están en uso.
- C) Fabric no elimina automáticamente `items` huérfanos después de un período fijo.
- D) Exportarlos a un recurso compartido de archivos los saca del marco de gobernanza y hace más difícil administrarlos.

</details>

---

### Pregunta 25

Necesitas asegurarte de que los datos en un lakehouse de Fabric estén cifrados en reposo. ¿Qué acción se requiere?

- A) Habilitar manualmente el cifrado en la configuración del lakehouse
- B) No se requiere ninguna acción; OneLake cifra los datos en reposo de forma predeterminada
- C) Cargar únicamente archivos previamente cifrados al lakehouse
- D) Configurar integración con Azure Key Vault para cada archivo

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) No se requiere ninguna acción; OneLake cifra los datos en reposo de forma predeterminada**

**Explicación:** OneLake cifra automáticamente todos los datos en reposo usando claves administradas por Microsoft. Este cifrado está habilitado de forma predeterminada y se aplica a todos los datos almacenados en OneLake, incluidos archivos de lakehouse, datos de warehouse y otros `items` de Fabric. No se necesita configuración adicional para el cifrado base en reposo.

**Por qué las otras opciones son incorrectas:**
- A) No existe un interruptor manual de cifrado; el cifrado en reposo es automático y siempre está activado.
- C) Cargar archivos previamente cifrados agrega complejidad innecesaria, ya que OneLake ya gestiona el cifrado.
- D) Aunque se pueden configurar claves administradas por el cliente mediante Azure Key Vault para mayor control, no es necesario para el cifrado básico en reposo.

</details>

---

### Pregunta 26

Un workspace de Fabric está conectado a un deployment pipeline. Necesitas aplicar distintos valores de parámetros, como cadenas de conexión de base de datos, para las etapas de Test y Production. ¿Cómo debes configurarlo?

- A) Editar manualmente los parámetros después de cada despliegue
- B) Usar deployment rules para definir sobrescrituras de parámetros por etapa
- C) Crear `branches` de Git separadas para cada conjunto de parámetros
- D) Almacenar los parámetros en una carpeta compartida de OneLake

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar deployment rules para definir sobrescrituras de parámetros por etapa**

**Explicación:** Los deployment pipelines admiten deployment rules que permiten definir sobrescrituras de valores de parámetros para cada etapa. Por ejemplo, puedes establecer una cadena de conexión de base de datos de desarrollo para la etapa de Test y una cadena de conexión de producción para la etapa de Production. Estas reglas se aplican automáticamente durante el despliegue.

**Por qué las otras opciones son incorrectas:**
- A) Editar manualmente los parámetros después de cada despliegue es propenso a errores y no escala.
- C) Las `branches` de Git administran versiones de código, no valores de parámetros de ejecución específicos por etapa dentro de deployment pipelines.
- D) Almacenar parámetros en OneLake no se integra con el mecanismo incorporado de sobrescritura de parámetros del deployment pipeline.

</details>

---

### Pregunta 27

Estás configurando monitoreo para tu entorno de Fabric. El CFO quiere un informe semanal de costos de consumo de capacity desglosados por workspace. ¿Cuál es el mejor enfoque?

- A) Revisar manualmente la página de facturación del Azure portal cada semana
- B) Usar la Microsoft Fabric Capacity Metrics app y crear un informe programado
- C) Pedir a cada propietario de workspace que reporte su propio uso
- D) Habilitar alertas de Azure Cost Management para el recurso de Fabric

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar la Microsoft Fabric Capacity Metrics app y crear un informe programado**

**Explicación:** La Capacity Metrics app proporciona métricas detalladas del uso de capacity desglosadas por workspace y tipo de carga de trabajo. Al construir un informe sobre estos datos y programar su entrega, puedes proporcionar al CFO resúmenes semanales consistentes y automatizados del consumo de costos sin esfuerzo manual.

**Por qué las otras opciones son incorrectas:**
- A) Revisar manualmente páginas de facturación no proporciona el desglose a nivel de workspace del consumo de Fabric capacity.
- C) El auto-reporte no es confiable y genera carga administrativa.
- D) Azure Cost Management proporciona datos de costos a nivel de suscripción, pero no desglosa el consumo de Fabric capacity a nivel de workspace con el mismo nivel de detalle.

</details>

---

### Pregunta 28

Un `data steward` necesita entender qué reports downstream se verán afectados si cambia el esquema de una tabla específica de lakehouse. ¿Qué funcionalidad debería usar?

- A) Impact analysis a través de data lineage
- B) Power BI Q&A
- C) Lista de acceso del workspace
- D) Capacity Metrics app

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Impact analysis a través de data lineage**

**Explicación:** El data lineage en Fabric proporciona capacidades de `impact analysis` que muestran todos los `items` downstream que dependen de una fuente de datos o item específico. Al examinar el lineage de una tabla de lakehouse, el `data steward` puede identificar cada semantic model, report y dashboard que se vería afectado por un cambio de esquema.

**Por qué las otras opciones son incorrectas:**
- B) Power BI Q&A habilita consultas en lenguaje natural sobre datos y no muestra dependencias entre `items`.
- C) La lista de acceso del workspace muestra quién tiene acceso al workspace, no dependencias de `items`.
- D) La Capacity Metrics app supervisa rendimiento y consumo, no dependencias de datos.

</details>

---

### Pregunta 29

Una organización tiene tanto Microsoft Fabric como un Azure Synapse Analytics workspace existente. Quieren consultar desde Fabric datos almacenados en su cuenta ADLS Gen2 vinculada a Synapse sin migrar esos datos. ¿Qué deberían usar?

- A) Crear un Fabric data pipeline para copiar datos cada noche
- B) Crear un OneLake shortcut que apunte a la cuenta ADLS Gen2
- C) Configurar database mirroring desde Synapse hacia Fabric
- D) Exportar los datos de Synapse a CSV y cargarlos en OneLake

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Crear un OneLake shortcut que apunte a la cuenta ADLS Gen2**

**Explicación:** Los OneLake shortcuts pueden referenciar almacenamiento ADLS Gen2 externo, haciendo que los datos sean accesibles dentro de Fabric como si fueran datos nativos de OneLake. Esto evita migración y duplicación de datos, a la vez que permite que las cargas de trabajo de Fabric consulten los datos en su lugar.

**Por qué las otras opciones son incorrectas:**
- A) Copiar datos cada noche crea duplicación y latencia, contradiciendo el requisito de evitar migración.
- C) Database mirroring replica datos operacionales de bases de datos, no archivos ADLS Gen2, hacia Fabric.
- D) Exportar manualmente a CSV y cargarlo en OneLake es ineficiente, propenso a errores y genera duplicación de datos.

</details>

---

### Pregunta 30

Una empresa exige que todas las acciones administrativas de Fabric se conserven durante siete años por cumplimiento normativo. ¿Cómo debe configurarse esto?

- A) Habilitar retención extendida de logs en el Fabric admin portal
- B) Configurar políticas de retención de audit logs en el Microsoft Purview compliance portal y exportar logs a almacenamiento de largo plazo
- C) Tomar capturas mensuales de la actividad del admin portal
- D) Asignar a un usuario para documentar manualmente todas las acciones administrativas

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Configurar políticas de retención de audit logs en el Microsoft Purview compliance portal y exportar logs a almacenamiento de largo plazo**

**Explicación:** Los Microsoft Purview audit logs capturan todas las actividades administrativas de Fabric. Para cumplir requisitos de retención a largo plazo, las organizaciones deben configurar políticas de retención en el Purview compliance portal y, opcionalmente, exportar los logs a Azure Storage o a una solución SIEM para archivado más allá del período de retención predeterminado.

**Por qué las otras opciones son incorrectas:**
- A) El Fabric admin portal no tiene una opción incorporada de retención de logs por siete años.
- C) Las capturas de pantalla no son auditables, buscables ni aceptables como control de cumplimiento.
- D) La documentación manual no es confiable, no es resistente a manipulación y no cumple requisitos regulatorios de auditoría.

</details>

---

### Pregunta 31

Tu tenant de Fabric tiene habilitada la organización a nivel de domains. ¿Qué beneficio proporciona asignar workspaces a domains?

- A) Los workspaces en un domain obtienen automáticamente cuotas de almacenamiento mayores
- B) Los domains permiten agrupación lógica de workspaces con políticas de gobernanza centralizadas y sensitivity labels predeterminadas
- C) Los domains reemplazan por completo la necesidad de roles de workspace
- D) Los domains permiten que los workspaces abarquen varias regiones de Azure

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Los domains permiten agrupación lógica de workspaces con políticas de gobernanza centralizadas y sensitivity labels predeterminadas**

**Explicación:** Los Fabric domains permiten a las organizaciones agrupar lógicamente workspaces por área de negocio, por ejemplo Finance o Marketing. Los domain admins pueden aplicar políticas de gobernanza, establecer sensitivity labels predeterminadas y administrar configuraciones de endorsement en todos los workspaces del domain, proporcionando una gobernanza coherente a nivel de unidad de negocio.

**Por qué las otras opciones son incorrectas:**
- A) Los domains no afectan cuotas de almacenamiento; el almacenamiento lo determina la capacity.
- C) Los roles de workspace siguen siendo el mecanismo principal de control de acceso dentro de workspaces individuales; los domains complementan, no reemplazan, esos roles.
- D) Los workspaces están ligados a la región de su capacity asignada; los domains no cambian límites regionales.

</details>

---

### Pregunta 32

Un administrador quiere asegurarse de que solo semantic models aprobados puedan usarse para crear reports en toda la organización. ¿Qué funcionalidad de gobernanza de Fabric admite esto?

- A) Endorsement (`Certified` y `Promoted`)
- B) Row-level security
- C) Permisos Viewer del workspace
- D) Capacity throttling

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Endorsement (`Certified` y `Promoted`)**

**Explicación:** Fabric admite labels de endorsement, `Certified` y `Promoted`, que marcan contenido confiable y aprobado. Los semantic models `Certified` son verificados por revisores designados, y las organizaciones pueden configurar políticas para fomentar o exigir que los usuarios construyan reports sobre contenido con endorsement, mejorando la calidad y consistencia de los datos.

**Por qué las otras opciones son incorrectas:**
- B) Row-level security restringe acceso a datos dentro de un `dataset`, pero no controla qué `datasets` se usan para reporting.
- C) Los permisos Viewer controlan quién puede ver `items`, no cuáles están aprobados para uso más amplio.
- D) Capacity throttling administra recursos de `compute` y no tiene relación con aprobación o endorsement de contenido.

</details>

---

### Pregunta 33

Estás configurando OneLake data access roles para un lakehouse. Quieres dar a un equipo acceso de solo lectura a una carpeta específica dentro del lakehouse, impidiendo el acceso a otras carpetas. ¿Qué debes configurar?

- A) Rol Viewer a nivel de workspace
- B) OneLake data access roles con permisos de lectura a nivel de carpeta
- C) Row-level security sobre el SQL analytics endpoint
- D) Una sensitivity label en las carpetas restringidas

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) OneLake data access roles con permisos de lectura a nivel de carpeta**

**Explicación:** Los OneLake data access roles proporcionan control de acceso detallado a nivel de carpeta dentro de un lakehouse. Al crear un rol que conceda permisos de lectura a una carpeta específica y asignar el equipo a ese rol, garantizas que solo puedan acceder a los datos designados mientras las demás carpetas permanecen inaccesibles.

**Por qué las otras opciones son incorrectas:**
- A) El rol Viewer concede acceso a todos los `items` visibles del workspace, no a carpetas específicas dentro de un lakehouse.
- C) Row-level security filtra filas en consultas, pero no controla acceso a carpetas del sistema de archivos del lakehouse.
- D) Las sensitivity labels clasifican datos, pero no aplican control de acceso a nivel de carpeta dentro de un lakehouse.

</details>

---

### Pregunta 34

Tu organización está evaluando SKUs de Fabric capacity. El equipo requiere `autoscaling` para que la capacity pueda aumentar temporalmente durante procesamiento en picos. ¿Qué funcionalidad deberían evaluar?

- A) Fabric capacity bursting y smoothing
- B) Actualizaciones manuales del SKU de capacity
- C) Agregar más licencias Power BI Pro
- D) Crear workspaces adicionales

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Fabric capacity bursting y smoothing**

**Explicación:** Las capacities de Fabric admiten `bursting`, lo que permite que las cargas de trabajo consuman temporalmente recursos de `compute` por encima de la asignación base del SKU. El `smoothing` distribuye el consumo en el tiempo para evitar `throttling` innecesario durante picos cortos. Juntas, estas funcionalidades proporcionan manejo elástico de procesamiento en picos sin requerir aumentos permanentes del SKU.

**Por qué las otras opciones son incorrectas:**
- B) Las actualizaciones manuales de SKU proporcionan aumentos permanentes y no están diseñadas para manejar picos temporales.
- C) Las licencias Power BI Pro son licencias por usuario y no afectan recursos de `compute` de la capacity.
- D) Crear workspaces adicionales no agrega capacity de `compute`; los workspaces comparten su capacity asignada.

</details>

---

### Pregunta 35

Un desarrollador hace accidentalmente `commit` de cadenas de conexión sensibles al repositorio Git conectado a un workspace de Fabric. ¿Qué acciones inmediatas deben tomarse?

- A) Eliminar el workspace y recrearlo
- B) Rotar las credenciales expuestas, eliminar los datos sensibles del historial de Git y revisar access logs
- C) Renombrar los parámetros de la cadena de conexión
- D) Agregar un aviso al README del repositorio

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Rotar las credenciales expuestas, eliminar los datos sensibles del historial de Git y revisar access logs**

**Explicación:** Cuando credenciales quedan registradas en un repositorio Git, la prioridad inmediata es rotar, es decir, cambiar, las credenciales expuestas para evitar acceso no autorizado, purgar los datos sensibles del historial de commits del repositorio y revisar los audit logs para determinar si las credenciales fueron accedidas por partes no autorizadas.

**Por qué las otras opciones son incorrectas:**
- A) Eliminar el workspace no soluciona las credenciales expuestas en el historial de Git y genera pérdida innecesaria de datos.
- C) Renombrar parámetros no revoca los valores de credenciales expuestos que siguen presentes en el historial de commits.
- D) Un aviso en el README no remedia la exposición de seguridad.

</details>

---

### Pregunta 36

Un administrador de Fabric necesita identificar qué cargas de trabajo consumieron más Capacity Units (CUs) durante la última semana. ¿En qué métrica de la Capacity Metrics app debería enfocarse?

- A) Número de usuarios del workspace
- B) Consumo de CU por tipo de carga de trabajo y por item
- C) Número total de reports publicados
- D) Número de `branches` Git activas

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Consumo de CU por tipo de carga de trabajo y por item**

**Explicación:** La Capacity Metrics app proporciona métricas detalladas de consumo de CU desglosadas por tipo de carga de trabajo, por ejemplo data warehouse, data engineering o Power BI, y por `items` individuales. Esta vista responde directamente qué cargas de trabajo están consumiendo más recursos de capacity en un período determinado.

**Por qué las otras opciones son incorrectas:**
- A) El número de usuarios del workspace no indica consumo de recursos.
- C) El número de reports publicados es una métrica de conteo que no refleja uso de `compute`.
- D) Las `branches` de Git son una construcción de desarrollo y no se rastrean como métrica de consumo de capacity.

</details>

---

### Pregunta 37

Tu organización usa Microsoft Fabric y quiere implementar una arquitectura de workspace hub-and-spoke. ¿Cuál es el propósito principal de este patrón?

- A) Consolidar todos los `items` en un único workspace por simplicidad
- B) Centralizar `datasets` compartidos en un workspace hub mientras los workspaces spoke contienen reports específicos por departamento
- C) Distribuir el almacenamiento uniformemente en todas las regiones de Azure
- D) Eliminar la necesidad de roles de workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Centralizar `datasets` compartidos en un workspace hub mientras los workspaces spoke contienen reports específicos por departamento**

**Explicación:** El patrón hub-and-spoke sitúa semantic models curados y activos de datos compartidos en un workspace hub central, mientras que cada departamento mantiene sus propios workspaces spoke para reports y análisis. Esto promueve reutilización de datos, consistencia y una única fuente de verdad, al tiempo que permite autonomía departamental.

**Por qué las otras opciones son incorrectas:**
- A) Consolidar todo en un único workspace genera desafíos de administración de permisos y no escala para organizaciones grandes.
- C) La distribución de almacenamiento entre regiones se administra mediante la ubicación de la capacity, no mediante patrones de arquitectura de workspace.
- D) Los roles de workspace siguen siendo necesarios tanto en workspaces hub como spoke para administrar acceso.

</details>

---

### Pregunta 38

Un administrador de workspace de Fabric quiere habilitar Trusted workspace access para que un workspace pueda acceder a datos en OneLake desde un item de Fabric usando una `managed identity`. ¿Qué debe configurarse?

- A) Un `service principal` en Microsoft Entra ID y un `client secret`
- B) La identidad del workspace debe estar habilitada y la cuenta de almacenamiento debe tener una `resource instance rule` que permita ese workspace
- C) Un token SAS en la cuenta de almacenamiento
- D) Un Power BI gateway en el workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) La identidad del workspace debe estar habilitada y la cuenta de almacenamiento debe tener una `resource instance rule` que permita ese workspace**

**Explicación:** Trusted workspace access aprovecha la `managed identity` del workspace para autenticarse con recursos externos. La identidad del workspace debe estar habilitada en Fabric, y la cuenta de almacenamiento de destino debe configurarse con una `resource instance rule` que confíe en el workspace de Fabric específico, permitiendo acceso seguro sin claves compartidas ni secretos.

**Por qué las otras opciones son incorrectas:**
- A) Los `service principals` con `client secrets` corresponden a otro patrón de autenticación; Trusted workspace access usa la identidad integrada del workspace.
- C) Los tokens SAS se basan en secretos compartidos y no aprovechan la `managed identity` del workspace.
- D) Los Power BI gateways sirven para conectarse a fuentes de datos on-premises, no para acceso basado en identidad entre workspace y almacenamiento.

</details>

---

### Pregunta 39

Un data engineer crea un OneLake shortcut desde un lakehouse de Fabric hacia un bucket de Amazon S3. ¿Qué método de autenticación se usa para acceder a los datos de S3?

- A) Microsoft Entra ID single sign-on
- B) Una conexión con una clave o un role ARN configurado mediante Fabric
- C) Windows Integrated Authentication
- D) OneLake sincroniza automáticamente credenciales con AWS IAM

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Una conexión con una clave o un role ARN configurado mediante Fabric**

**Explicación:** Al crear un shortcut hacia Amazon S3, Fabric requiere una conexión que especifique la autenticación al bucket S3. Esto puede usar una `access key` y `secret key` o un IAM role ARN. La conexión se configura en Fabric y almacena las credenciales de forma segura para que el shortcut las use.

**Por qué las otras opciones son incorrectas:**
- A) Microsoft Entra ID SSO no es compatible para autenticación cross-cloud hacia AWS S3 mediante shortcuts.
- C) Windows Integrated Authentication es para entornos Windows on-premises, no para acceso a almacenamiento entre nubes.
- D) OneLake no sincroniza automáticamente con AWS IAM; se requiere configuración explícita de credenciales.

</details>

---

### Pregunta 40

Un equipo de cumplimiento exige que todos los reports de Power BI en el workspace de Finance muestren una marca de agua `"Confidential"` al visualizarse. ¿Qué enfoque logra esto?

- A) Agregar manualmente una marca de agua con cuadro de texto a cada report
- B) Aplicar una Microsoft Purview sensitivity label con políticas de marcado visual
- C) Usar un tema de Power BI con fondo de marca de agua
- D) Configurar row-level security para mostrar una fila de marca de agua

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Aplicar una Microsoft Purview sensitivity label con políticas de marcado visual**

**Explicación:** Las Microsoft Purview sensitivity labels pueden configurarse con políticas de marcado de contenido que incluyen encabezados, pies de página y marcas de agua. Cuando se aplica a los reports una label `"Confidential"` con marcado visual, el marcado se muestra automáticamente, garantizando cumplimiento consistente sin intervención manual.

**Por qué las otras opciones son incorrectas:**
- A) Agregar cuadros de texto manualmente no escala y puede ser eliminado por editores de reports.
- C) Los temas de Power BI controlan el estilo visual, pero no aplican marcas de agua de cumplimiento que persistan con el contenido.
- D) Row-level security filtra filas de datos y no puede renderizar marcas de agua visuales en reports.

</details>

---

### Pregunta 41

Estás implementando una estrategia de recuperación ante desastres para cargas de trabajo críticas de Fabric. ¿Qué enfoque se alinea con las prácticas recomendadas por Microsoft?

- A) Mantener un respaldo manual de todos los archivos `.pbix` en una unidad de red local
- B) Usar Git integration para control de versiones de definiciones de `items` del workspace y aprovechar la redundancia integrada de OneLake
- C) Crear capturas diarias de todos los reports para propósitos de recuperación
- D) Depender únicamente de la papelera de reciclaje para recuperar `items`

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar Git integration para control de versiones de definiciones de `items` del workspace y aprovechar la redundancia integrada de OneLake**

**Explicación:** Git integration proporciona un historial recuperable de definiciones de `items` del workspace que pueden volver a desplegarse si los `items` se pierden o corrompen. OneLake proporciona redundancia de datos integrada mediante replicación de almacenamiento de Azure. Juntos, estos mecanismos forman una base robusta de recuperación ante desastres alineada con las prácticas recomendadas por Microsoft.

**Por qué las otras opciones son incorrectas:**
- A) Los respaldos manuales de `.pbix` en una unidad de red no son completos para todos los tipos de `items` de Fabric y tienden a quedar desactualizados.
- C) Las capturas de reports no pueden usarse para restaurar reports o datos funcionales.
- D) La papelera tiene retención limitada y no protege contra todos los escenarios de desastre.

</details>

---

### Pregunta 42

Un administrador quiere evitar que los usuarios creen Fabric trial capacities en el tenant. ¿Dónde debería deshabilitarse esto?

- A) Azure subscription cost management
- B) Tenant settings del Fabric admin portal
- C) Microsoft Entra ID license management
- D) Configuración individual del perfil del usuario

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Tenant settings del Fabric admin portal**

**Explicación:** El Fabric admin portal incluye tenant settings que controlan si los usuarios pueden iniciar Fabric trial capacities. Al deshabilitar esta opción, o restringirla a grupos de seguridad específicos, los administradores evitan la creación no autorizada de trial capacities y mantienen control sobre el panorama de capacities del tenant.

**Por qué las otras opciones son incorrectas:**
- A) Azure cost management rastrea gasto, pero no controla la creación de Fabric trial capacities.
- C) Microsoft Entra ID license management administra asignación de licencias, pero no controla directamente el aprovisionamiento de trials de Fabric.
- D) La configuración individual del usuario no incluye la capacidad de controlar creación de trial capacities a nivel de tenant.

</details>

---

### Pregunta 43

Un data engineer quiere usar un notebook de Fabric para leer datos desde un lakehouse ubicado en otro workspace. ¿Qué se requiere para este acceso cross-workspace?

- A) El data engineer debe ser Admin del workspace en ambos workspaces
- B) El data engineer necesita al menos permisos de lectura sobre el lakehouse en el otro workspace y puede referenciarlo mediante su ruta
- C) El acceso cross-workspace no es posible en Fabric
- D) El lakehouse debe exportarse y reimportarse al workspace del ingeniero

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) El data engineer necesita al menos permisos de lectura sobre el lakehouse en el otro workspace y puede referenciarlo mediante su ruta**

**Explicación:** Fabric admite acceso cross-workspace a datos. Un usuario con al menos permisos de lectura, como el rol Viewer o permisos de lectura a nivel de item, sobre un lakehouse en otro workspace puede referenciar la ruta de OneLake de ese lakehouse desde un notebook, habilitando consumo de datos entre workspaces de forma fluida.

**Por qué las otras opciones son incorrectas:**
- A) No se requiere acceso Admin; los permisos de lectura son suficientes para consumir datos.
- C) El acceso cross-workspace es una capacidad admitida en Fabric.
- D) Exportar y reimportar datos es innecesario dada la capacidad integrada de acceso cross-workspace de Fabric.

</details>

---

### Pregunta 44

Tu organización tiene una Fabric capacity asignada a la región East US. Un nuevo proyecto requiere un workspace, pero el equipo del proyecto está basado en West Europe. ¿Qué deberías considerar respecto a la creación del workspace?

- A) El workspace se creará automáticamente en West Europe según la ubicación del usuario
- B) El workspace residirá en la región East US porque hereda la región de la capacity; considera aprovisionar una nueva capacity en West Europe para menor latencia
- C) Los workspaces no están vinculados a ninguna región
- D) El equipo debe usar una VPN para acceder al workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) El workspace residirá en la región East US porque hereda la región de la capacity; considera aprovisionar una nueva capacity en West Europe para menor latencia**

**Explicación:** Los workspaces en Fabric residen en la región de Azure de su capacity asignada. Si la capacity está en East US, los datos y el `compute` del workspace estarán en East US, lo que puede resultar en mayor latencia para usuarios de West Europe. Aprovisionar una capacity en West Europe y asignar allí el workspace reduciría latencia y también puede ayudar con requisitos de `data residency`.

**Por qué las otras opciones son incorrectas:**
- A) La región del workspace se determina por la capacity, no por la ubicación física del usuario.
- C) Los workspaces siempre están vinculados a la región de su capacity asignada.
- D) Una VPN no cambia la región física de los datos ni mejora significativamente la latencia entre regiones para servicios de Fabric.

</details>

---

### Pregunta 45

Una organización quiere automatizar el despliegue de `items` de workspace de Fabric usando prácticas de CI/CD. ¿Qué combinación de funcionalidades de Fabric lo soporta mejor?

- A) Git integration y deployment pipelines
- B) Solo Power BI REST APIs
- C) Exportación e importación manual de archivos `.pbix`
- D) Flujos de aprobación por correo electrónico

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Git integration y deployment pipelines**

**Explicación:** Git integration proporciona control de versiones para `items` del workspace, habilitando cambios versionados y revisables. Los deployment pipelines automatizan la promoción de contenido a través de etapas de Development, Test y Production. Juntas, estas funcionalidades forman un flujo completo de CI/CD para Fabric, soportando despliegue automatizado y gobernado de contenido.

**Por qué las otras opciones son incorrectas:**
- B) Las Power BI REST APIs pueden usarse para automatización, pero no proporcionan el flujo integrado de CI/CD basado en Git nativo de Fabric.
- C) Las operaciones manuales sobre archivos no son automatizadas ni soportan principios de CI/CD.
- D) Las aprobaciones por correo electrónico no son un mecanismo de automatización y no se integran con control de versiones ni con procesos de despliegue.

</details>

---

### Pregunta 46

Un tenant tiene múltiples Fabric capacities. Un administrador quiere mover un workspace de una capacity a otra. ¿Qué consideración es importante?

- A) Todos los usuarios perderán acceso permanente al workspace
- B) Los datos del workspace se moverán a la región de la nueva capacity si las capacities están en regiones distintas, lo que puede afectar `data residency`
- C) Mover un workspace entre capacities no está soportado
- D) El workspace debe estar vacío antes de poder reasignarse

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Los datos del workspace se moverán a la región de la nueva capacity si las capacities están en regiones distintas, lo que puede afectar `data residency`**

**Explicación:** Cuando un workspace se reasigna a una capacity en una región diferente, los datos del workspace migran a la nueva región. Esta es una consideración crítica para organizaciones con requisitos de `data residency`, ya que mover datos entre regiones puede violar obligaciones regulatorias.

**Por qué las otras opciones son incorrectas:**
- A) Los usuarios conservan acceso según sus roles de workspace; la reasignación de capacity no elimina permisos de usuario.
- C) Mover workspaces entre capacities es una operación admitida en Fabric.
- D) Los workspaces pueden reasignarse a una capacity diferente independientemente de su contenido.

</details>

---

### Pregunta 47

Un administrador de Fabric quiere configurar alertas cuando la utilización de capacity supere el 80% durante más de 30 minutos. ¿Cuál es el enfoque recomendado?

- A) Revisar manualmente la Capacity Metrics app cada 30 minutos
- B) Configurar alertas usando las capacidades de monitoreo y alertas de capacity, o usar métricas de Azure Monitor
- C) Pedir a los usuarios que informen por correo electrónico cuando noten lentitud
- D) Configurar la capacity para apagarse automáticamente al 80% de utilización

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Configurar alertas usando las capacidades de monitoreo y alertas de capacity, o usar métricas de Azure Monitor**

**Explicación:** Las métricas de Fabric capacity pueden integrarse con Azure Monitor para crear reglas de alerta basadas en umbrales de utilización. Los administradores pueden configurar alertas que se disparen cuando la utilización supere el 80% durante un período sostenido, permitiendo una respuesta proactiva a la presión sobre la capacity antes de que los usuarios sufran degradación significativa del rendimiento.

**Por qué las otras opciones son incorrectas:**
- A) La revisión manual no escala ni es confiable para monitoreo continuo.
- C) Las alertas reportadas por usuarios son reactivas e inconsistentes.
- D) Las capacities de Fabric no tienen una funcionalidad de apagado automático por umbral de utilización, y apagarlas sería disruptivo.

</details>

---

### Pregunta 48

Tienes la tarea de asegurar que un workspace específico de Fabric siga una convención de nombres para todos los `items`, por ejemplo que todos los semantic models deban comenzar con `"SM_"`. ¿Cuál es la mejor manera de aplicarlo?

- A) Configurar una regla de nombres incorporada en Fabric dentro de la configuración del workspace
- B) Establecer una política de gobernanza con documentación y revisiones manuales periódicas, o usar scripts de automatización mediante APIs
- C) Usar sensitivity labels para codificar convenciones de nombres
- D) Restringir la creación de `items` únicamente a Admins del workspace

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Establecer una política de gobernanza con documentación y revisiones manuales periódicas, o usar scripts de automatización mediante APIs**

**Explicación:** Fabric actualmente no tiene una funcionalidad incorporada para imponer convenciones de nombres. El enfoque recomendado es documentar estándares de nomenclatura en una política de gobernanza, comunicarlos al equipo y, opcionalmente, aplicarlos mediante automatización usando las Fabric REST APIs para auditar o renombrar `items` que no cumplan.

**Por qué las otras opciones son incorrectas:**
- A) No existe una funcionalidad incorporada de regla de nombres en la configuración del workspace de Fabric.
- C) Las sensitivity labels clasifican sensibilidad de datos, no convenciones de nombres.
- D) Restringir la creación solo a Admins limita la productividad y no hace cumplir por sí mismo las convenciones de nombres.

</details>

---

### Pregunta 49

Una gran empresa está incorporándose a Fabric y quiere asegurarse de que los datos de cada unidad de negocio queden aislados a nivel de almacenamiento, incluso dentro del mismo tenant. ¿Qué enfoque proporciona el aislamiento de datos más fuerte?

- A) Asignar a cada unidad de negocio su propio workspace con diferentes roles de workspace
- B) Asignar a cada unidad de negocio su propia capacity y configurar OneLake data access roles para control detallado
- C) Colocar todos los datos en un solo lakehouse con row-level security
- D) Usar archivos Power BI Desktop separados para cada unidad de negocio

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Asignar a cada unidad de negocio su propia capacity y configurar OneLake data access roles para control detallado**

**Explicación:** Capacities separadas proporcionan aislamiento de `compute` y facturación, mientras que los OneLake data access roles aplican control de acceso a nivel de almacenamiento en carpetas y `items`. Esta combinación ofrece el aislamiento más fuerte dentro de un solo tenant, garantizando que los datos de una unidad de negocio no puedan ser accedidos por otra en la capa de almacenamiento.

**Por qué las otras opciones son incorrectas:**
- A) Los roles de workspace proporcionan control de acceso, pero no ofrecen el mismo nivel de aislamiento a nivel de almacenamiento que capacities separadas combinadas con OneLake data access roles.
- C) Row-level security filtra resultados de consultas, pero no impide acceso directo a nivel de archivo sobre los datos subyacentes en OneLake.
- D) Los archivos Power BI Desktop son herramientas locales de autoría y no representan una estrategia de aislamiento de almacenamiento en Fabric.

</details>

---

### Pregunta 50

Un administrador de Fabric necesita revisar qué usuarios externos, es decir cuentas `guest`, tienen acceso a workspaces de Fabric y qué roles poseen. ¿Cuál es el método más efectivo?

- A) Revisar solo la lista de usuarios guest en Microsoft Entra ID
- B) Usar el Fabric admin portal para revisar listas de acceso de workspace y contrastarlas con las cuentas guest de Microsoft Entra ID
- C) Enviar una encuesta a todos los administradores de workspace preguntando por usuarios externos
- D) Deshabilitar preventivamente todo el acceso guest

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar el Fabric admin portal para revisar listas de acceso de workspace y contrastarlas con las cuentas guest de Microsoft Entra ID**

**Explicación:** El Fabric admin portal proporciona a los administradores visibilidad sobre la membresía de workspaces en todo el tenant. Al revisar las listas de acceso de los workspaces y contrastarlas con el directorio de usuarios guest de Microsoft Entra ID, los administradores pueden obtener una visión completa de qué usuarios externos tienen acceso y qué roles se les asignaron.

**Por qué las otras opciones son incorrectas:**
- A) La lista de usuarios guest de Microsoft Entra ID muestra quién tiene una cuenta guest, pero no a qué workspaces de Fabric pueden acceder ni sus roles.
- C) Las encuestas no son confiables y consumen tiempo, sin garantía de respuestas completas o precisas.
- D) Deshabilitar todo el acceso guest es disruptivo y puede romper flujos legítimos de colaboración externa.

</details>

---
