# Dominio 2: Preguntas de practica - Preparar y servir datos

> **50 preguntas de opcion multiple** que cubren el Dominio 2 del examen DP-600 (40-45% de ponderacion).
> Este es el dominio mas grande; domina estos temas para maximizar tu puntuacion.
> Cada pregunta incluye una explicacion detallada de la respuesta.

---

### Pregunta 1

Un data engineer necesita ingerir archivos CSV que llegan diariamente a un contenedor de Azure Data Lake Storage Gen2 en un Fabric Lakehouse. Cada archivo puede tener hasta 2 GB y debe aterrizar en la capa Bronze sin transformacion. Que enfoque es el MAS eficiente?

- A) Usar un Dataflow Gen2 para leer y escribir los archivos CSV
- B) Usar una Copy Activity en un Data Factory pipeline
- C) Escribir un notebook de PySpark para leer y escribir los archivos
- D) Crear un shortcut al contenedor de ADLS Gen2

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: D) Crear un shortcut al contenedor de ADLS Gen2**

**Explicacion:** Los Shortcuts en Microsoft Fabric proporcionan una referencia virtualizada y zero-copy a origenes de datos externos. Como el requisito es aterrizar los datos en la capa Bronze sin transformacion, un shortcut elimina por completo la necesidad de mover datos, por lo que es la opcion mas eficiente. Los datos permanecen en su ubicacion original mientras aparecen como una carpeta nativa en el Lakehouse.

**Por que las otras opciones son incorrectas:**
- A) Dataflow Gen2 esta pensado para escenarios de transformacion de datos y agrega una sobrecarga innecesaria para una ingesta simple sin transformacion.
- B) Copy Activity moveria fisicamente los datos, consumiendo compute y almacenamiento cuando en realidad no se necesita movimiento.
- C) Un notebook de PySpark agrega sobrecarga de desarrollo y compute que no es necesaria para un aterrizaje sin transformacion.

</details>

---

### Pregunta 2

Estas construyendo una medallion architecture en Microsoft Fabric. Los datos fluyen de Bronze (raw) -> Silver (cleansed) -> Gold (aggregated). Que formato de archivo se usa por defecto al escribir datos en tablas de Lakehouse?

- A) Parquet
- B) CSV
- C) Delta Lake (Parquet + transaction log)
- D) Avro

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Delta Lake (Parquet + transaction log)**

**Explicacion:** Los Fabric Lakehouses usan Delta Lake como formato de tabla predeterminado. Delta Lake almacena los datos como archivos Parquet con un transaction log adicional (`_delta_log`) que proporciona transacciones ACID, schema enforcement y capacidades de time travel. Todas las managed tables en un Fabric Lakehouse son tablas Delta por defecto.

**Por que las otras opciones son incorrectas:**
- A) Aunque los archivos de datos subyacentes son Parquet, el formato de tabla incluye el Delta transaction log; Parquet simple por si solo no describe el panorama completo.
- B) CSV no es un formato de managed table en el Lakehouse y carece de schema enforcement y garantias transaccionales.
- D) Avro no es el formato predeterminado para tablas de Lakehouse en Fabric.

</details>

---

### Pregunta 3

Un data engineer esta configurando un Data Factory pipeline para copiar datos desde un SQL Server on-premises hacia un Fabric Lakehouse. La red on-premises no permite conexiones entrantes. Que se debe instalar para habilitar la conectividad?

- A) Una Fabric capacity en la misma region que el SQL Server
- B) Un on-premises data gateway
- C) Una conexion VPN a OneLake
- D) Azure ExpressRoute con private endpoints

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Un on-premises data gateway**

**Explicacion:** Un on-premises data gateway crea una conexion saliente segura desde la red local hacia Microsoft Fabric, permitiendo la transferencia de datos sin requerir reglas de firewall entrantes. El gateway actua como un puente, retransmitiendo datos desde el SQL Server local hacia la nube mediante conexiones salientes cifradas.

**Por que las otras opciones son incorrectas:**
- A) La ubicacion regional de la Fabric capacity no resuelve los requisitos de conectividad de red on-premises.
- C) OneLake no admite conexiones VPN como metodo nativo de conectividad para origenes de datos on-premises.
- D) Aunque ExpressRoute es una opcion valida de conectividad en Azure, no es requerida y es mucho mas compleja y costosa que un on-premises data gateway para este escenario.

</details>

---

### Pregunta 4

Necesitas cargar 500 millones de filas desde un area de staging a una tabla de Fabric Warehouse lo mas rapido posible. Que sentencia T-SQL proporciona el MEJOR rendimiento de carga masiva?

- A) INSERT INTO ... SELECT
- B) COPY INTO
- C) BULK INSERT
- D) MERGE

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) COPY INTO**

**Explicacion:** La sentencia COPY INTO esta optimizada para cargas masivas de alto rendimiento en tablas de Fabric Warehouse. Admite lecturas paralelas desde archivos de origen, distribucion eficiente de datos y es el metodo recomendado para cargar grandes volumenes de datos. Supera a las operaciones INSERT fila por fila o por conjuntos en escenarios de carga masiva.

**Por que las otras opciones son incorrectas:**
- A) INSERT INTO ... SELECT funciona, pero no esta optimizado para carga masiva a la escala de 500 millones de filas.
- C) BULK INSERT es una caracteristica de SQL Server que no esta disponible en Fabric Warehouse.
- D) MERGE esta disenado para operaciones upsert (insert/update/delete) y no es optimo para cargas puramente masivas.

</details>

---

### Pregunta 5

Una empresa minorista transmite transacciones de punto de venta a Microsoft Fabric en tiempo real. Necesitan capturar estos eventos para analitica downstream. Que componente de Fabric deberian usar para la ingesta de eventos en tiempo real?

- A) Dataflow Gen2
- B) Data Factory pipeline con un scheduled trigger
- C) Eventstream
- D) Power BI streaming dataset

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Eventstream**

**Explicacion:** Eventstreams en Microsoft Fabric estan disenados especificamente para capturar, transformar y enrutar datos de eventos en tiempo real. Se integran con origenes como Azure Event Hubs, IoT Hub y aplicaciones personalizadas, por lo que son la opcion correcta para transmitir transacciones de punto de venta a Fabric para analitica downstream.

**Por que las otras opciones son incorrectas:**
- A) Dataflow Gen2 es una herramienta ETL orientada a batch y no admite ingesta streaming en tiempo real.
- B) Un scheduled trigger ejecuta el pipeline a intervalos fijos y no es streaming en tiempo real real.
- D) Los Power BI streaming datasets estan limitados a escenarios de visualizacion y no sirven como capa general de ingesta de eventos para analitica.

</details>

---

### Pregunta 6

En un Fabric Lakehouse, escribes datos en la seccion `Tables` usando un notebook de Spark. Despues, notas que los datos tambien aparecen en el SQL analytics endpoint. Por que?

- A) El SQL analytics endpoint almacena en cache automaticamente todas las salidas de Spark
- B) Las managed Delta tables en la seccion Tables se registran automaticamente en el SQL analytics endpoint
- C) Debes registrar manualmente cada tabla en el SQL analytics endpoint
- D) Los datos se duplican mediante una Copy Activity en segundo plano

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Las managed Delta tables en la seccion Tables se registran automaticamente en el SQL analytics endpoint**

**Explicacion:** Cuando escribes datos como managed Delta tables en la seccion Tables del Lakehouse, estas se detectan automaticamente y se exponen a traves del SQL analytics endpoint. Esto proporciona una experiencia de consulta T-SQL de solo lectura sin requerir registro manual ni duplicacion de datos. El SQL analytics endpoint simplemente referencia los mismos archivos Delta subyacentes.

**Por que las otras opciones son incorrectas:**
- A) El SQL analytics endpoint no almacena datos en cache; lee directamente desde los archivos Delta almacenados en OneLake.
- C) No se requiere registro manual para las managed tables en la seccion Tables.
- D) No ocurre duplicacion de datos; el SQL analytics endpoint lee desde los mismos archivos de Delta Lake.

</details>

---

### Pregunta 7

Un data engineer esta disenando un Dataflow Gen2 para limpiar y transformar datos de clientes antes de cargarlos en una tabla de Lakehouse. Que lenguaje de consulta usa Dataflow Gen2 para definir las transformaciones?

- A) T-SQL
- B) Python
- C) Power Query M
- D) Spark SQL

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Power Query M**

**Explicacion:** Dataflow Gen2 usa Power Query M como lenguaje de transformacion, proporcionando una experiencia low-code/no-code mediante el editor de Power Query. Es el mismo motor y lenguaje usado en Power BI dataflows y Power Query en Excel, por lo que resulta familiar para un amplio rango de usuarios. Las transformaciones se definen visualmente o en codigo M.

**Por que las otras opciones son incorrectas:**
- A) T-SQL se usa en Fabric Warehouse y en el SQL analytics endpoint, no en Dataflow Gen2.
- B) Python se usa en notebooks de Spark, no en transformaciones de Dataflow Gen2.
- D) Spark SQL se usa dentro de notebooks de Spark y consultas de Lakehouse, no en Dataflow Gen2.

</details>

---

### Pregunta 8

Estas cargando datos en un Fabric Warehouse usando COPY INTO desde archivos Parquet almacenados en ADLS Gen2. Los archivos Parquet contienen una columna llamada `event_timestamp` que no existe en la tabla de destino. Que sucede cuando ejecutas la sentencia COPY INTO?

- A) La sentencia tiene exito e ignora la columna adicional
- B) La sentencia falla con un error de schema mismatch
- C) La columna adicional se agrega automaticamente a la tabla de destino
- D) La sentencia tiene exito pero carga NULL en todas las columnas

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) La sentencia tiene exito e ignora la columna adicional**

**Explicacion:** COPY INTO en Fabric Warehouse asigna columnas de origen a columnas de destino por posicion ordinal (por defecto) o por nombre. Las columnas adicionales en el origen que no se asignan a la tabla de destino se ignoran sin producir error. Este comportamiento permite cargas flexibles desde archivos que pueden contener campos adicionales.

**Por que las otras opciones son incorrectas:**
- B) COPY INTO no exige coincidencia estricta de schema; las columnas extra del origen simplemente se omiten.
- C) COPY INTO no altera el schema de la tabla de destino; solo carga datos en columnas existentes.
- D) Las columnas asignadas se cargan correctamente; solo se ignoran las columnas extra no asignadas.

</details>

---

### Pregunta 9

Un data engineer necesita crear una tabla de dimension en un Fabric Warehouse que se unira frecuentemente con una tabla fact grande. La dimension tiene aproximadamente 50 000 filas. Que estrategia de distribucion deberia elegir?

- A) Distribucion hash sobre la join key
- B) Distribucion round-robin
- C) Distribucion replicada
- D) Range partitioning sobre la join key

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Distribucion replicada**

**Explicacion:** Para tablas de dimension pequenas, la distribucion replicada copia la tabla completa en cada nodo de compute. Esto elimina el movimiento de datos durante los joins con tablas fact grandes, mejorando significativamente el rendimiento de las consultas. Con solo 50 000 filas, el costo extra de almacenamiento por replicacion es despreciable.

**Por que las otras opciones son incorrectas:**
- A) La distribucion hash es mas adecuada para tablas fact grandes donde ubicar filas por la join key reduce operaciones de shuffle.
- B) Round-robin distribuye las filas uniformemente, pero requiere movimiento de datos durante los joins porque las claves coincidentes no estan co-ubicadas.
- D) Range partitioning es una estrategia de organizacion de datos dentro de una tabla, no una estrategia de distribucion entre nodos.

</details>

---

### Pregunta 10

Tienes una tabla Delta en tu Lakehouse con particiones diarias. Con el tiempo, se han acumulado muchos archivos pequenos en cada particion debido a appends frecuentes. Las consultas se estan volviendo lentas. Que deberias hacer?

- A) Eliminar y recrear la tabla
- B) Ejecutar el comando OPTIMIZE sobre la tabla Delta
- C) Convertir la tabla a formato Parquet
- D) Aumentar el tamano del cluster de Spark

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Ejecutar el comando OPTIMIZE sobre la tabla Delta**

**Explicacion:** El comando OPTIMIZE compacta archivos pequenos en archivos mas grandes, reduciendo la cantidad de archivos que deben leerse durante las consultas. Esta es la solucion estandar para el "small file problem" en Delta Lake. Mejora el rendimiento de lectura sin requerir reconstruir la tabla y preserva el historial y la metadata de la tabla.

**Por que las otras opciones son incorrectas:**
- A) Eliminar y recrear la tabla es destructivo e innecesario cuando OPTIMIZE resuelve el problema directamente.
- C) Convertir a Parquet haria perder caracteristicas de Delta Lake como transacciones ACID, time travel y schema enforcement.
- D) Aumentar el tamano del cluster agrega costo sin abordar la causa raiz de la sobrecarga por archivos pequenos.

</details>

---

### Pregunta 11

En la medallion architecture, cual es el proposito PRINCIPAL de la capa Silver?

- A) Almacenar datos raw exactamente como se ingieren desde los sistemas de origen
- B) Almacenar datos limpios, validados y deduplicados que se ajustan a un enterprise data model
- C) Almacenar datos preagregados optimizados para reporting de negocio
- D) Archivar datos historicos para fines de cumplimiento

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Almacenar datos limpios, validados y deduplicados que se ajustan a un enterprise data model**

**Explicacion:** La capa Silver en la medallion architecture sirve como la capa de datos limpios y conformados. Los datos procedentes de Bronze se validan, se deduplican y se transforman hacia un enterprise data model consistente. Esta capa proporciona una fuente confiable y fiable para el procesamiento posterior hacia la capa Gold.

**Por que las otras opciones son incorrectas:**
- A) Almacenar datos raw exactamente como llegan describe la capa Bronze, no la Silver.
- C) Los datos preagregados optimizados para reporting describen la capa Gold.
- D) El archivado para cumplimiento no es el proposito principal de ninguna capa medallion especifica; las politicas de retencion abarcan varias capas.

</details>

---

### Pregunta 12

Un data engineer escribe un notebook de PySpark para ingerir archivos JSON en un Lakehouse. Los archivos JSON ocasionalmente contienen campos nuevos que no estaban presentes en archivos anteriores. El engineer quiere que la tabla acepte automaticamente estos campos nuevos. Que opcion de Spark deberia configurar?

- A) `mergeSchema = true`
- B) `overwriteSchema = true`
- C) `enforceSchema = false`
- D) `autoSchema = true`

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) `mergeSchema = true`**

**Explicacion:** Establecer `mergeSchema` en `true` (o usar `.option("mergeSchema", "true")`) le indica a Delta Lake que combine automaticamente el schema de los datos nuevos con el schema existente de la tabla. Las columnas nuevas de los datos entrantes se agregan a la tabla sin sobrescribir el schema existente. Este es el enfoque correcto para schemas evolutivos.

**Por que las otras opciones son incorrectas:**
- B) `overwriteSchema = true` reemplaza todo el schema de la tabla con el schema de los datos nuevos, lo que podria eliminar columnas existentes.
- C) `enforceSchema = false` no es una opcion valida de Delta Lake.
- D) `autoSchema = true` no es una configuracion reconocida de Delta Lake ni de Spark.

</details>

---

### Pregunta 13

Necesitas construir un pipeline en Data Factory que ejecute una serie de activities: primero copiar datos raw, luego ejecutar un notebook para transformarlos y finalmente refrescar un semantic model. Si el notebook falla, el pipeline debe enviar una notificacion por correo electronico. Que caracteristica del pipeline maneja esta logica condicional?

- A) Parameters
- B) Activity dependencies con condiciones de fallo
- C) Variables
- D) Bucles ForEach

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Activity dependencies con condiciones de fallo**

**Explicacion:** Los Data Factory pipelines admiten activity dependencies donde configuras la condicion bajo la cual se ejecuta una activity downstream: Success, Failure, Completion o Skipped. Al establecer que la activity de notificacion por correo dependa del notebook con una condicion de "Failure", el correo se envia solo cuando el notebook falla.

**Por que las otras opciones son incorrectas:**
- A) Parameters pasan valores al pipeline pero no controlan el flujo de ejecucion condicional entre activities.
- C) Variables almacenan valores intermedios durante la ejecucion del pipeline, pero no definen condiciones de dependencia entre activities.
- D) Los bucles ForEach iteran sobre colecciones de elementos y no manejan logica de ramificacion basada en fallos.

</details>

---

### Pregunta 14

Un data engineer crea un shortcut en un Fabric Lakehouse que apunta a un bucket de Amazon S3. Que ocurre con los datos en S3 cuando se crea el shortcut?

- A) Los datos se copian a OneLake para acceso local
- B) Los datos permanecen en S3; el shortcut proporciona una referencia virtualizada
- C) Los datos se mueven permanentemente de S3 a OneLake
- D) Solo se copia la metadata; los datos se almacenan en cache en OneLake en el primer acceso

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Los datos permanecen en S3; el shortcut proporciona una referencia virtualizada**

**Explicacion:** Los Shortcuts en Fabric son punteros que crean una referencia virtualizada a ubicaciones de almacenamiento externas como Amazon S3, ADLS Gen2 o Google Cloud Storage. No se copian ni mueven datos cuando se crea un shortcut. Las consultas sobre el shortcut leen los datos directamente desde la ubicacion de origen, convirtiendolo en una integracion zero-copy.

**Por que las otras opciones son incorrectas:**
- A) Los Shortcuts no copian datos; acceden a ellos en su ubicacion de origen.
- C) Los Shortcuts nunca mueven datos; los datos de origen permanecen intactos.
- D) Los Shortcuts no almacenan los datos en cache en el primer acceso; cada consulta lee desde el origen. Puede haber caching a nivel de compute, pero no es comportamiento del shortcut.

</details>

---

### Pregunta 15

Estas ejecutando un comando COPY INTO para cargar datos en una tabla de Fabric Warehouse. Que formatos de archivo se admiten como archivos de origen? (Elige la MEJOR respuesta.)

- A) Solo Parquet y CSV
- B) Parquet, CSV y JSON
- C) Parquet, CSV, JSON y Avro
- D) Cualquier formato admitido por Azure Data Factory

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Solo Parquet y CSV**

**Explicacion:** La sentencia COPY INTO en Fabric Warehouse admite Parquet y CSV (texto delimitado) como formatos de archivo de origen. Son los dos formatos que pueden cargarse directamente usando COPY INTO. Para otros formatos, los datos deben convertirse primero a un formato admitido usando un notebook o Dataflow Gen2 antes de cargarse.

**Por que las otras opciones son incorrectas:**
- B) JSON no es un formato de origen admitido directamente por COPY INTO en Fabric Warehouse.
- C) Avro tampoco es un formato de origen admitido por COPY INTO.
- D) COPY INTO es una sentencia T-SQL con soporte especifico de formatos, no equivalente a las capacidades mas amplias de Data Factory.

</details>

---

### Pregunta 16

Un notebook de Spark lee un archivo CSV sin encabezados y lo escribe en una tabla Delta. El engineer observa que todas las columnas se llaman `_c0`, `_c1`, `_c2`, etc. Que deberia hacer para asignar nombres de columna significativos?

- A) Establecer `.option("header", "true")` al leer el CSV
- B) Usar el metodo `.toDF("col1", "col2", "col3")` o definir un schema
- C) Renombrar las columnas en el SQL analytics endpoint despues de la carga
- D) Editar el archivo CSV para agregar encabezados antes de ingerirlo

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar el metodo `.toDF("col1", "col2", "col3")` o definir un schema**

**Explicacion:** Cuando un archivo CSV no tiene encabezados, Spark asigna nombres de columna predeterminados (`_c0`, `_c1`, etc.). El engineer puede renombrar columnas usando `.toDF()` con los nombres deseados o proporcionar un schema predefinido usando `StructType` al leer el archivo. Ambos enfoques asignan nombres de columna significativos durante el paso de lectura/transformacion.

**Por que las otras opciones son incorrectas:**
- A) Establecer `header=true` en un CSV sin encabezados interpretaria incorrectamente la primera fila de datos como nombres de columna, perdiendo datos.
- C) El SQL analytics endpoint proporciona acceso de solo lectura a las tablas de Lakehouse y no admite renombrar columnas.
- D) Modificar los archivos de origen es poco practico y no resuelve el problema de forma programatica.

</details>

---

### Pregunta 17

Tu organizacion exige que todos los datos en la capa Gold del Lakehouse esten optimizados para un rendimiento rapido de informes Power BI. Que tecnica de optimizacion especifica de Fabric deberias aplicar a las tablas Delta de la capa Gold?

- A) Z-ordering en todas las columnas
- B) Optimizacion V-Order
- C) Bloom filter indexing
- D) Compresion columnar con gzip

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Optimizacion V-Order**

**Explicacion:** V-Order es una optimizacion en tiempo de escritura especifica de Microsoft Fabric que ordena y organiza los datos dentro de archivos Parquet para maximizar el rendimiento de lectura para Power BI y otros motores analiticos. V-Order esta habilitado por defecto en Fabric y esta disenado especificamente para acelerar el rendimiento de consultas para cargas BI que leen desde OneLake.

**Por que las otras opciones son incorrectas:**
- A) Z-ordering en todas las columnas seria contraproducente; Z-ordering es efectivo sobre un pequeno numero de columnas filtradas frecuentemente, no sobre todas.
- C) Bloom filter indexing ayuda en busquedas puntuales, pero no es la optimizacion principal especifica de Fabric para rendimiento BI.
- D) Gzip es un codec de compresion general y no una tecnica de optimizacion especifica de Fabric para rendimiento en Power BI.

</details>

---

### Pregunta 18

Un pipeline necesita copiar datos desde 20 tablas de origen distintas en una base de datos SQL hacia 20 tablas correspondientes de Lakehouse. Cual es el diseno de pipeline MAS eficiente?

- A) Crear 20 Copy Activities separadas, una para cada tabla
- B) Usar una activity ForEach con una Copy Activity dentro, iterando sobre una lista de nombres de tablas
- C) Crear 20 pipelines separados y usar un pipeline maestro para invocarlos
- D) Usar una sola Copy Activity con un nombre de tabla wildcard

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar una activity ForEach con una Copy Activity dentro, iterando sobre una lista de nombres de tablas**

**Explicacion:** La activity ForEach permite iterar sobre una coleccion (por ejemplo, una lista de nombres de tablas) y ejecutar una Copy Activity para cada elemento. Este patron es conciso, mantenible y admite ejecucion paralela dentro del bucle. Agregar una tabla nueva requiere solo actualizar la lista, no modificar la estructura del pipeline.

**Por que las otras opciones son incorrectas:**
- A) Crear 20 Copy Activities individuales genera un diseno de pipeline desordenado y dificil de mantener.
- C) Crear 20 pipelines separados agrega complejidad y sobrecarga de gestion innecesarias.
- D) Copy Activity no admite nombres de tabla wildcard para copiar multiples tablas en una sola activity.

</details>

---

### Pregunta 19

Creas una tabla Delta particionada por `region` en un Fabric Lakehouse. Una consulta filtra por `order_date` pero no por `region`. Como afecta el particionamiento a esta consulta?

- A) La consulta se beneficia del partition pruning sobre `order_date`
- B) La consulta escanea todas las particiones, sin obtener beneficio del esquema de particionamiento
- C) La consulta falla porque no filtra por la columna de particion
- D) Delta Lake vuelve a particionar automaticamente la tabla segun el filtro de la consulta

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) La consulta escanea todas las particiones, sin obtener beneficio del esquema de particionamiento**

**Explicacion:** El partition pruning solo ocurre cuando la consulta filtra sobre la columna de particion. Como la tabla esta particionada por `region` pero la consulta filtra por `order_date`, Spark no puede eliminar ninguna particion. Deben escanearse todos los directorios de particion, por lo que el particionamiento no aporta mejora de rendimiento para este patron de consulta en particular.

**Por que las otras opciones son incorrectas:**
- A) El partition pruning requiere un filtro sobre la columna de particion (`region`), no sobre `order_date`.
- C) Las consultas no fallan por omitir la columna de particion; simplemente leen todas las particiones.
- D) Delta Lake no vuelve a particionar tablas dinamicamente segun patrones de consulta.

</details>

---

### Pregunta 20

Un data engineer necesita implementar una slowly changing dimension (SCD) Type 2 en un Fabric Lakehouse usando un notebook. Que operacion de Delta Lake es la MAS adecuada para esto?

- A) INSERT INTO
- B) UPDATE
- C) MERGE INTO
- D) DELETE seguido de INSERT

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) MERGE INTO**

**Explicacion:** MERGE INTO (tambien conocido como upsert) es la operacion ideal para implementar SCD Type 2. Permite comparar registros entrantes con registros existentes y, en una sola operacion atomica, insertar registros nuevos, actualizar registros existentes (por ejemplo, establecer fechas de fin) e insertar nuevas versiones de registros modificados. Esto coincide perfectamente con el patron SCD Type 2.

**Por que las otras opciones son incorrectas:**
- A) INSERT INTO solo agrega filas nuevas y no puede actualizar registros existentes para cerrar versiones antiguas de la dimension.
- B) UPDATE por si solo no puede insertar nuevas filas versionadas para registros de dimension modificados.
- D) DELETE seguido de INSERT no es atomico, arriesga perdida de datos en caso de fallo y no preserva las versiones historicas requeridas por SCD Type 2.

</details>

---

### Pregunta 21

Estas disenando una solucion de streaming analytics. Sensores IoT envian telemetria a Azure Event Hubs. Necesitas procesar estos datos casi en tiempo real y aterrizarlos en una tabla Delta de Lakehouse. Que caracteristica de Fabric deberias usar?

- A) Un Data Factory pipeline programado para ejecutarse cada minuto
- B) Un Eventstream conectado al Event Hub con un destino Lakehouse
- C) Un Dataflow Gen2 con una programacion de refresh de un minuto
- D) Un dashboard en tiempo real de Power BI conectado a Event Hubs

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Un Eventstream conectado al Event Hub con un destino Lakehouse**

**Explicacion:** Eventstreams se conectan de forma nativa a Azure Event Hubs y pueden enrutar datos streaming directamente hacia una tabla Delta de Lakehouse. Esto proporciona procesamiento casi en tiempo real, con baja latencia, ingesta continua y transformaciones opcionales en flujo. Es la solucion nativa de Fabric para escenarios streaming-to-Lakehouse.

**Por que las otras opciones son incorrectas:**
- A) Un pipeline programado cada minuto es micro-batch, no streaming real, y agrega complejidad y latencia innecesarias.
- C) Dataflow Gen2 no admite refresh con intervalos menores a un minuto y no esta disenado para cargas streaming.
- D) Un dashboard en tiempo real de Power BI es una herramienta de visualizacion, no un mecanismo de ingesta para aterrizar datos en un Lakehouse.

</details>

---

### Pregunta 22

Un data engineer escribe un notebook de Spark que lee datos desde una tabla Bronze, aplica transformaciones y escribe a una tabla Silver. El notebook debe ejecutarse todos los dias a las 6 AM. Como deberia programarlo?

- A) Usar el scheduler de Spark integrado en el notebook
- B) Incrustar el notebook en un Data Factory pipeline y agregar un schedule trigger
- C) Usar Windows Task Scheduler en una VM
- D) Configurar una Azure Logic App para llamar al API del notebook

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Incrustar el notebook en un Data Factory pipeline y agregar un schedule trigger**

**Explicacion:** El enfoque recomendado en Fabric es orquestar la ejecucion de notebooks mediante Data Factory pipelines. Al agregar el notebook como una activity del pipeline y configurar un schedule trigger diario a las 6 AM, obtienes programacion fiable, monitoreo, logica de reintento e integracion con otras activities, todo dentro del ecosistema Fabric.

**Por que las otras opciones son incorrectas:**
- A) Los notebooks de Fabric no tienen un scheduler integrado para ejecucion recurrente.
- C) Usar Windows Task Scheduler en una VM introduce infraestructura externa y sobrecarga de gestion fuera de Fabric.
- D) Azure Logic Apps agrega complejidad innecesaria cuando Fabric Data Factory ya proporciona capacidades nativas de programacion.

</details>

---

### Pregunta 23

Tienes un Fabric Warehouse con una tabla fact que contiene 10 mil millones de filas. La tabla esta hash-distributed sobre `customer_id`. Los analistas ejecutan con frecuencia consultas que filtran por `order_date`. Que deberias agregar para mejorar el rendimiento de consultas filtradas por fecha?

- A) Cambiar la distribucion a hash sobre `order_date`
- B) Agregar un clustered columnstore index sobre `order_date`
- C) Crear una particion de tabla sobre `order_date` (por ejemplo, por mes o por anio)
- D) Crear una materialized view agrupada por `order_date`

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Crear una particion de tabla sobre `order_date` (por ejemplo, por mes o por anio)**

**Explicacion:** Particionar la tabla fact por `order_date` habilita partition elimination cuando las consultas filtran por rangos de fechas. Esto reduce drasticamente la cantidad de datos escaneados en consultas filtradas por fecha. La distribucion hash sobre `customer_id` se mantiene para el rendimiento de joins, mientras que el particionamiento optimiza el patron comun de filtro por fecha.

**Por que las otras opciones son incorrectas:**
- A) Cambiar la distribucion a `order_date` perjudicaria el rendimiento de joins sobre `customer_id` y no ofrece el mismo beneficio de pruning que el particionamiento.
- B) Fabric Warehouse usa almacenamiento columnstore automaticamente; no puedes crear manualmente clustered columnstore indexes.
- D) Una materialized view podria ayudar en consultas de agregacion especificas, pero no optimiza de manera general consultas arbitrarias filtradas por fecha tan eficazmente como el particionamiento.

</details>

---

### Pregunta 24

Un Dataflow Gen2 esta configurado para cargar datos en una tabla de Lakehouse. Durante la ejecucion falla con un error que indica que el schema de la tabla de destino no coincide con los datos de origen. Cual es la causa MAS probable?

- A) El Lakehouse esta en modo de solo lectura
- B) El output mapping de Dataflow Gen2 tiene nombres de columna o tipos de datos que no coinciden con la tabla existente
- C) Dataflow Gen2 no admite escribir en tablas de Lakehouse
- D) La Fabric capacity esta en pausa

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) El output mapping de Dataflow Gen2 tiene nombres de columna o tipos de datos que no coinciden con la tabla existente**

**Explicacion:** Cuando Dataflow Gen2 escribe en una tabla de Lakehouse existente, los nombres y tipos de datos de las columnas de salida deben ser compatibles con el schema de la tabla de destino. Si los nombres difieren o los tipos son incompatibles (por ejemplo, escribir un string en una columna integer), la operacion falla con un error de schema mismatch. Debe verificarse el mapping en el paso de salida del Dataflow.

**Por que las otras opciones son incorrectas:**
- A) Las tablas de Lakehouse no tienen un modo de solo lectura que impida escrituras desde Dataflow Gen2.
- C) Dataflow Gen2 admite plenamente la escritura a tablas de Lakehouse como destino.
- D) Una capacity en pausa impediria toda ejecucion, no produciria un error de schema mismatch.

</details>

---

### Pregunta 25

En un Fabric Lakehouse, cual es la diferencia entre la seccion `Tables` y la seccion `Files`?

- A) Tables almacena datos relacionales; Files almacena solo imagenes y documentos
- B) Tables contiene managed Delta tables consultables via SQL; Files contiene archivos no administrados en cualquier formato
- C) Tables y Files son alias de la misma ubicacion de almacenamiento
- D) Tables es solo para datos estructurados; Files es solo para datos semiestructurados

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Tables contiene managed Delta tables consultables via SQL; Files contiene archivos no administrados en cualquier formato**

**Explicacion:** La seccion Tables de un Lakehouse contiene managed Delta tables que se registran automaticamente en el SQL analytics endpoint y pueden consultarse usando T-SQL. La seccion Files es un area de almacenamiento de uso general donde puedes guardar archivos en cualquier formato (CSV, JSON, Parquet, imagenes, etc.) sin que se traten como managed tables.

**Por que las otras opciones son incorrectas:**
- A) Files puede contener cualquier tipo de archivo, no solo imagenes y documentos; incluye CSV, JSON, Parquet y mas.
- C) Tables y Files son areas de almacenamiento distintas dentro del Lakehouse con comportamientos y gestion de metadata diferentes.
- D) Ambas secciones pueden contener datos estructurados; la diferencia esta en el registro como managed table, no en la estructura del dato.

</details>

---

### Pregunta 26

Necesitas aplicar reglas de calidad de datos a los datos entrantes antes de que lleguen a la capa Silver. Que enfoque proporciona schema enforcement en la capa de almacenamiento en un Fabric Lakehouse?

- A) Crear reglas de validacion en Power BI
- B) Confiar en el schema enforcement de Delta Lake al escribir en tablas Delta
- C) Agregar CHECK constraints en el SQL analytics endpoint
- D) Usar reglas de calidad de datos de Azure Purview

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Confiar en el schema enforcement de Delta Lake al escribir en tablas Delta**

**Explicacion:** Delta Lake aplica schema enforcement en escritura por defecto. Cuando se escriben datos nuevos en una tabla Delta, deben coincidir con el schema de la tabla en nombres de columna, tipos de datos y nulabilidad. Los registros que violan el schema se rechazan, impidiendo que datos incorrectos entren en la tabla. Este es el mecanismo principal de schema enforcement a nivel de almacenamiento en un Lakehouse.

**Por que las otras opciones son incorrectas:**
- A) Las reglas de validacion de Power BI operan en la capa de reporting, no en la capa de almacenamiento de datos.
- C) El SQL analytics endpoint es de solo lectura para tablas de Lakehouse y no admite agregar constraints.
- D) Azure Purview proporciona gobernanza y catalogacion, pero no aplica schema enforcement al momento de escribir en un Lakehouse.

</details>

---

### Pregunta 27

Un data engineer usa un notebook de Spark para escribir datos en una tabla Delta con modo `overwrite`. Despues de que la escritura termina, nota que el schema de los datos nuevos era diferente al de la tabla original. Que le ocurrio a la tabla?

- A) La escritura fallo porque el schema no coincidia
- B) La tabla se sobrescribio con los datos nuevos, pero se preservo el schema original
- C) La tabla se sobrescribio con los datos nuevos y el schema original, a menos que `overwriteSchema` se haya establecido en true
- D) La tabla se elimino y se recreo automaticamente

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) La tabla se sobrescribio con los datos nuevos y el schema original, a menos que `overwriteSchema` se haya establecido en true**

**Explicacion:** En Delta Lake, cuando se usa modo `overwrite`, los datos nuevos deben ajustarse al schema existente de la tabla por defecto. Si los datos nuevos tienen un schema diferente, la escritura falla a menos que se especifique `.option("overwriteSchema", "true")`. Con `overwriteSchema` habilitado, el schema de la tabla es reemplazado por el schema de los datos nuevos.

**Por que las otras opciones son incorrectas:**
- A) La escritura falla solo si el schema difiere y `overwriteSchema` no esta configurado, pero la pregunta dice que la escritura se completo.
- B) El schema no se preserva automaticamente si `overwriteSchema` esta habilitado; el schema nuevo reemplaza al anterior.
- D) Las tablas Delta no se eliminan ni recrean; el modo overwrite reemplaza los datos dentro de la estructura existente.

</details>

---

### Pregunta 28

Tienes un Data Factory pipeline que copia datos desde una REST API. El API devuelve resultados paginados con un campo `nextLink` en cada respuesta. Como deberias configurar la Copy Activity para manejar la paginacion?

- A) Usar un bucle ForEach para iterar manualmente por las paginas
- B) Configurar las reglas de paginacion en la configuracion de origen de la Copy Activity
- C) Crear varias Copy Activities, una por pagina
- D) Escribir un notebook personalizado para manejar la paginacion

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Configurar las reglas de paginacion en la configuracion de origen de la Copy Activity**

**Explicacion:** La Copy Activity en Data Factory admite reglas de paginacion integradas para origenes REST API. Puedes configurarla para seguir automaticamente el `nextLink` (u otro token de paginacion similar), leyendo todas las paginas hasta que no haya mas datos. Esto elimina la necesidad de logica manual de bucles y mantiene el diseno del pipeline simple.

**Por que las otras opciones son incorrectas:**
- A) Un bucle ForEach agrega complejidad innecesaria cuando la Copy Activity ya tiene soporte nativo de paginacion.
- C) Crear una Copy Activity por pagina es impractico cuando el numero total de paginas es desconocido o variable.
- D) Un notebook personalizado es excesivo cuando la Copy Activity soporta paginacion REST de forma nativa.

</details>

---

### Pregunta 29

Un data engineer necesita consultar datos tanto de un Fabric Lakehouse como de un Fabric Warehouse dentro del mismo workspace. Que enfoque permite cross-database queries?

- A) Crear shortcuts entre el Lakehouse y el Warehouse
- B) Usar cross-database queries en el SQL analytics endpoint con nombres de tres partes
- C) Exportar los datos de ambos a un archivo CSV compartido
- D) Esto no es posible en Microsoft Fabric

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar cross-database queries en el SQL analytics endpoint con nombres de tres partes**

**Explicacion:** Microsoft Fabric admite cross-database queries usando nombres de tres partes (`database.schema.table`) en el SQL analytics endpoint. Esto te permite unir datos desde el SQL analytics endpoint de un Lakehouse y un Warehouse en una sola consulta T-SQL sin mover ni duplicar datos.

**Por que las otras opciones son incorrectas:**
- A) Los Shortcuts hacen referencia a ubicaciones de almacenamiento externas, no a otros items de Fabric como Warehouses dentro del mismo workspace.
- C) Exportar a CSV es ineficiente y hace perder los beneficios de los motores analiticos.
- D) Las cross-database queries son totalmente compatibles en Fabric usando convenciones de nombres de tres partes.

</details>

---

### Pregunta 30

Cuando usas la sentencia COPY INTO en Fabric Warehouse, que metodo de autenticacion se usa para acceder a archivos en OneLake?

- A) Tokens Shared Access Signature (SAS)
- B) Service principal con client secret
- C) Organizational identity (pass-through authentication)
- D) Storage account access keys

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Organizational identity (pass-through authentication)**

**Explicacion:** Cuando COPY INTO accede a archivos almacenados en OneLake, utiliza la organizational identity (Microsoft Entra ID) del usuario o servicio que ejecuta el comando. Como OneLake es el almacenamiento nativo de Fabric, la autenticacion se maneja mediante la gestion de identidad integrada de Fabric, no mediante credenciales externas como SAS tokens o access keys.

**Por que las otras opciones son incorrectas:**
- A) Los SAS tokens son un mecanismo de Azure Storage que no se usa para acceso interno a OneLake en Fabric.
- B) Los service principals con client secrets no son el metodo de autenticacion predeterminado para COPY INTO accediendo a OneLake.
- D) Las storage account access keys no aplican a OneLake, que usa autenticacion basada en Microsoft Entra ID.

</details>

---

### Pregunta 31

Un equipo de data engineering esta construyendo una capa Bronze en su Lakehouse. Reciben datos de 15 sistemas de origen distintos en varios formatos (CSV, JSON, Parquet, XML). Cual es el enfoque recomendado para la capa Bronze?

- A) Transformar todos los datos a un formato estandar unico antes de almacenarlos en Bronze
- B) Almacenar los datos en su formato original con transformacion minima o nula
- C) Ingerir solo los datos que pasen los controles de calidad
- D) Almacenar todos los datos como CSV para compatibilidad universal

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Almacenar los datos en su formato original con transformacion minima o nula**

**Explicacion:** La capa Bronze en la medallion architecture esta disenada para capturar datos raw de los sistemas de origen con transformacion minima o nula. Esto preserva la fidelidad original de los datos, permite reprocesarlos si cambia la logica de transformacion y proporciona una trazabilidad completa. La limpieza y estandarizacion se realizan en la capa Silver.

**Por que las otras opciones son incorrectas:**
- A) Transformar los datos antes de almacenarlos en Bronze contradice el proposito de tener una capa raw para auditabilidad y reprocesamiento.
- C) Filtrar datos por controles de calidad en Bronze arriesga perder datos de origen que podrian necesitarse despues para investigacion o reprocesamiento.
- D) Convertir todo a CSV haria perder informacion de schema de formatos estructurados como Parquet y agregaria procesamiento innecesario.

</details>

---

### Pregunta 32

Tienes una tabla Delta que se actualizo accidentalmente con datos incorrectos hace 3 horas. Necesitas restaurar la tabla a su estado anterior a esa mala actualizacion. Que caracteristica de Delta Lake lo permite?

- A) Soft delete recovery
- B) Time travel usando `VERSION AS OF` o `TIMESTAMP AS OF`
- C) OneLake recycle bin
- D) Backup and restore desde Azure Backup

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Time travel usando `VERSION AS OF` o `TIMESTAMP AS OF`**

**Explicacion:** Delta Lake mantiene un transaction log que registra cada cambio en la tabla. Time travel permite consultar o restaurar una tabla a cualquier version anterior usando `RESTORE TABLE table_name TO VERSION AS OF <version>` o `RESTORE TABLE table_name TO TIMESTAMP AS OF <timestamp>`. Este es el mecanismo integrado para recuperarse de cambios accidentales en los datos.

**Por que las otras opciones son incorrectas:**
- A) Delta Lake no tiene una caracteristica de "soft delete recovery"; la recuperacion se realiza mediante time travel y el comando RESTORE.
- C) OneLake no proporciona recycle bin para recuperacion a nivel de tabla tras actualizaciones incorrectas.
- D) Azure Backup no esta integrado con tablas Delta en Fabric para recuperacion point-in-time.

</details>

---

### Pregunta 33

Un data engineer escribe un notebook de Spark para procesar un dataset de 500 GB. El notebook se queda sin memoria y falla. Que enfoque es MAS probable que resuelva el problema sin aumentar recursos del cluster?

- A) Hacer cache de todo el dataset en memoria usando `.cache()`
- B) Reparticionar los datos en mas particiones y procesarlos de forma incremental
- C) Convertir los datos a formato CSV para tamanos de archivo mas pequenos
- D) Usar `.collect()` para llevar los datos al driver y procesarlos localmente

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Reparticionar los datos en mas particiones y procesarlos de forma incremental**

**Explicacion:** Reparticionar distribuye los datos en mas particiones, reduciendo la memoria requerida por particion durante el procesamiento. Procesar de forma incremental (por ejemplo, por rango de fechas o por particion) reduce aun mas la presion de memoria. Este enfoque funciona dentro de los recursos existentes del cluster optimizando como Spark distribuye el trabajo entre executors.

**Por que las otras opciones son incorrectas:**
- A) Hacer cache de los 500 GB completos en memoria empeoraria el problema de memoria, no lo resolveria.
- C) Los archivos CSV suelen ser mas grandes que Parquet/Delta debido a la falta de compresion y almacenamiento columnar, empeorando los problemas de memoria.
- D) Usar `.collect()` lleva todo el dataset al nodo driver, lo que provocaria inmediatamente un error de out-of-memory en ese nodo unico.

</details>

---

### Pregunta 34

Estas creando un Data Factory pipeline que debe ejecutar distintas activities segun el valor de un parametro del pipeline. Por ejemplo, si `source_type` es "SQL", ejecutar Copy Activity A; si es "API", ejecutar Copy Activity B. Que constructo del pipeline deberias usar?

- A) Activity ForEach
- B) Activity If Condition (Switch o If)
- C) Activity Lookup
- D) Activity Set Variable

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Activity If Condition (Switch o If)**

**Explicacion:** La activity If Condition evalua una expresion booleana y ejecuta distintas ramas segun el resultado (True/False). Para multiples valores discretos, la activity Switch puede evaluar un parametro contra varios casos. Ambos constructos permiten rutas de ejecucion condicional segun el valor de un parametro del pipeline.

**Por que las otras opciones son incorrectas:**
- A) ForEach itera sobre colecciones y no proporciona ramificacion condicional basada en valores de parametros.
- C) Lookup recupera datos desde un origen, pero no implementa logica de ejecucion condicional.
- D) Set Variable asigna valores, pero no controla que activities se ejecutan segun condiciones.

</details>

---

### Pregunta 35

Un data engineer necesita asegurarse de que no se inserten registros duplicados cuando un notebook de Spark se vuelve a ejecutar debido a un reintento del pipeline. Los datos de origen contienen un `transaction_id` unico. Que estrategia evita duplicados?

- A) Usar modo `append` y confiar en que Delta Lake detecte duplicados automaticamente
- B) Usar `MERGE INTO` con una condicion de coincidencia sobre `transaction_id`
- C) Usar modo `overwrite` para reemplazar todos los datos en cada ejecucion
- D) Agregar una unique constraint sobre `transaction_id` en la tabla Delta

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Usar `MERGE INTO` con una condicion de coincidencia sobre `transaction_id`**

**Explicacion:** MERGE INTO realiza una operacion upsert: inserta registros cuando no encuentra coincidencia en `transaction_id` y actualiza (o omite) cuando existe una coincidencia. Esto hace que la operacion sea idempotente: volver a ejecutar el notebook produce el mismo resultado sin crear registros duplicados. Es el patron estandar para manejar reintentos en data pipelines.

**Por que las otras opciones son incorrectas:**
- A) Delta Lake no detecta ni evita automaticamente filas duplicadas en modo `append`; simplemente agrega todas las filas entrantes.
- C) El modo `overwrite` reemplaza todos los datos existentes, lo que podria provocar perdida de datos si el notebook procesa solo un subconjunto del total.
- D) Delta Lake no admite unique constraints; la unicidad debe aplicarse mediante logica de aplicacion como MERGE.

</details>

---

### Pregunta 36

Necesitas crear una view en un Fabric Warehouse que combine datos de tres tablas y aplique logica de negocio. Los analistas deberian poder consultar esta view para reporting. Que tipo de view deberias crear?

- A) Una SQL view estandar
- B) Una materialized view
- C) Una indexed view
- D) Una temporary view

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Una SQL view estandar**

**Explicacion:** Una SQL view estandar en Fabric Warehouse encapsula la logica de joins y de negocio en un objeto reutilizable y consultable. Los analistas pueden consultar la view como si fuera una tabla. Las views estandar son la forma mas directa de abstraer consultas complejas y presentar una interfaz simplificada para reporting.

**Por que las otras opciones son incorrectas:**
- B) Aunque existen materialized views en Fabric Warehouse, la pregunta pide una view de proposito general para reporting; una view estandar es suficiente y no requiere almacenamiento adicional ni administracion de refresh.
- C) Las indexed views son un concepto de SQL Server que no esta disponible en Fabric Warehouse.
- D) Las temporary views tienen alcance de sesion y no estarian disponibles para otros analistas.

</details>

---

### Pregunta 37

Una tabla Delta en tu Lakehouse ha acumulado entradas antiguas del transaction log y archivos de datos de versiones anteriores. La tabla ha crecido y consume almacenamiento excesivo. Que deberias ejecutar para recuperar espacio?

- A) TRUNCATE TABLE
- B) VACUUM
- C) DROP TABLE y recrearla
- D) DBCC SHRINKDATABASE

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) VACUUM**

**Explicacion:** El comando VACUUM elimina archivos de datos antiguos que ya no estan referenciados por la version actual de la tabla Delta (mas alla del periodo de retencion configurado). Esto recupera almacenamiento consumido por archivos de versiones anteriores, registros eliminados y archivos compactados que ya no necesita el transaction log.

**Por que las otras opciones son incorrectas:**
- A) TRUNCATE TABLE elimina todos los datos actuales de la tabla, pero no limpia versiones historicas de archivos.
- C) Eliminar y recrear la tabla es destructivo, hace perder el historial y es innecesario cuando VACUUM resuelve la limpieza.
- D) DBCC SHRINKDATABASE es un comando de SQL Server que no esta disponible en Fabric Lakehouse ni en Spark.

</details>

---

### Pregunta 38

Un Eventstream en Fabric esta recibiendo eventos JSON desde Azure Event Hubs. Necesitas filtrar el stream para conservar solo los eventos donde `event_type = 'purchase'` antes de enviar los datos al Lakehouse. Donde deberias aplicar este filtro?

- A) En el propio Event Hub usando consumer groups
- B) En el Eventstream usando una transformacion en flujo
- C) En el Lakehouse despues de que los datos hayan aterrizado
- D) En la aplicacion de origen antes de enviar los eventos

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) En el Eventstream usando una transformacion en flujo**

**Explicacion:** Eventstreams admiten transformaciones en flujo que pueden filtrar, proyectar y agregar datos antes de que lleguen al destino. Aplicar un filtro para `event_type = 'purchase'` dentro del Eventstream reduce el volumen de datos escritos en el Lakehouse y evita almacenar eventos no deseados, haciendo el pipeline mas eficiente.

**Por que las otras opciones son incorrectas:**
- A) Los consumer groups de Event Hub controlan que consumidores leen del hub, pero no admiten filtrado basado en contenido.
- C) Filtrar despues de aterrizar en el Lakehouse desperdicia almacenamiento y compute al ingerir primero datos no deseados.
- D) Modificar la aplicacion de origen podria no ser viable y desplaza la logica de data engineering fuera de la plataforma analitica.

</details>

---

### Pregunta 39

Tienes un notebook de Spark que lee de una tabla Delta y escribe resultados en otra tabla Delta. Quieres asegurarte de que tanto la lectura como la escritura usen una instantanea consistente de los datos. Que caracteristica de Delta Lake proporciona esto?

- A) Row-level locking
- B) Transacciones ACID con snapshot isolation
- C) Optimistic locking con lecturas pesimistas
- D) Bloqueos de lectura/escritura a nivel de archivo

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Transacciones ACID con snapshot isolation**

**Explicacion:** Delta Lake proporciona transacciones ACID con snapshot isolation, lo que significa que cada operacion de lectura ve una instantanea consistente de la tabla al inicio de la transaccion. Los escritores concurrentes no afectan la vista del lector. Esto garantiza que el notebook lea un estado consistente y escriba resultados de forma atomica, sin interferencia de operaciones concurrentes.

**Por que las otras opciones son incorrectas:**
- A) Delta Lake no usa row-level locking; usa concurrencia optimista con atomicidad a nivel de archivo.
- C) Delta Lake usa control de concurrencia optimista, no lecturas pesimistas.
- D) Delta Lake no implementa bloqueos de lectura/escritura a nivel de archivo; la consistencia se garantiza mediante el transaction log y snapshot isolation.

</details>

---

### Pregunta 40

Un data engineer necesita crear una tabla de capa Gold que preagregue ventas diarias por producto y tienda. Esta tabla se refrescara cada noche. Donde deberia residir esta tabla?

- A) En la seccion Files del Lakehouse como archivo Parquet
- B) En la seccion Tables del Lakehouse como managed Delta table
- C) En un Power BI dataset separado
- D) En una Azure SQL Database fuera de Fabric

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) En la seccion Tables del Lakehouse como managed Delta table**

**Explicacion:** Las tablas agregadas de la capa Gold deberian almacenarse como managed Delta tables en la seccion Tables del Lakehouse. Esto proporciona transacciones ACID, schema enforcement, registro automatico en el SQL analytics endpoint e integracion fluida con Power BI mediante DirectLake. Las managed Delta tables son la eleccion estandar para datos Gold en Fabric.

**Por que las otras opciones son incorrectas:**
- A) Los archivos Parquet en la seccion Files no son managed tables y carecen de capacidad de consulta SQL, soporte transaccional y registro automatico en el endpoint.
- C) Un Power BI dataset es un semantic model para reporting, no una capa de almacenamiento para datos agregados.
- D) Una Azure SQL Database externa introduce complejidad innecesaria y saca los datos del ecosistema Fabric.

</details>

---

### Pregunta 41

Estas disenando un esquema de Fabric Warehouse para un modelo star schema. Cual afirmacion sobre Fabric Warehouse es VERDADERA?

- A) Debes definir primary key y foreign key constraints para la optimizacion de consultas
- B) Las primary key y foreign key constraints son solo informativas y no se aplican
- C) Fabric Warehouse no admite ningun tipo de constraints
- D) Las foreign key constraints se aplican, pero las primary key constraints no

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Las primary key y foreign key constraints son solo informativas y no se aplican**

**Explicacion:** En Fabric Warehouse, las primary key y foreign key constraints pueden definirse, pero son informativas (no aplicadas). El query optimizer puede usarlas para generar mejores planes de ejecucion, pero el motor de base de datos no impide violaciones. La integridad de datos debe garantizarse mediante logica ETL en lugar de depender de constraints aplicadas.

**Por que las otras opciones son incorrectas:**
- A) Las constraints no necesitan definirse para optimizacion, aunque pueden ayudar; de todos modos no se aplican.
- C) Fabric Warehouse si admite definir constraints, pero son solo informativas.
- D) Ni las primary key ni las foreign key constraints se aplican en Fabric Warehouse.

</details>

---

### Pregunta 42

Un notebook usa `spark.read.format("delta").load("/lakehouse/default/Tables/sales")` y la lectura tarda mas de 10 minutos para una tabla de 50 GB. El engineer sospecha una mala organizacion de archivos. Que DOS acciones mejorarian MAS el rendimiento de lectura? (Elige la MEJOR respuesta unica.)

- A) Ejecutar OPTIMIZE con ZORDER sobre las columnas filtradas con mayor frecuencia
- B) Convertir la tabla de Delta a Parquet
- C) Aumentar el numero de Spark executors a 100
- D) Mover la tabla a la seccion Files

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Ejecutar OPTIMIZE con ZORDER sobre las columnas filtradas con mayor frecuencia**

**Explicacion:** OPTIMIZE compacta archivos pequenos en otros mas grandes, reduciendo la sobrecarga a nivel de archivo. Agregar ZORDER sobre columnas filtradas con frecuencia co-ubica datos relacionados dentro de los archivos, habilitando data skipping durante la lectura. Juntas, estas mejoras reducen la cantidad de archivos y el volumen de datos escaneados, mejorando dramaticamente el rendimiento de lectura para consultas filtradas.

**Por que las otras opciones son incorrectas:**
- B) Convertir a Parquet elimina beneficios de Delta Lake como transacciones ACID y time travel, y no corrige la organizacion de archivos.
- C) Agregar mas executors puede ayudar en paralelismo, pero no corrige el problema subyacente de small files o mala distribucion de datos.
- D) Mover la tabla a la seccion Files elimina caracteristicas de managed table y no mejora la organizacion de archivos.

</details>

---

### Pregunta 43

Un data engineer crea un pipeline con la siguiente secuencia de activities: Lookup -> ForEach -> Copy Activity (dentro de ForEach) -> Notebook. El Lookup devuelve 100 elementos. Cuantas veces se ejecuta la activity Notebook?

- A) 1 vez
- B) 100 veces
- C) Depende de si el Notebook esta dentro o fuera del ForEach
- D) 0 veces porque los Notebooks no pueden seguir a las activities ForEach

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Depende de si el Notebook esta dentro o fuera del ForEach**

**Explicacion:** Si la activity Notebook esta dentro del bucle ForEach, se ejecuta una vez por iteracion (100 veces). Si el Notebook esta fuera del bucle ForEach (secuenciado despues), se ejecuta exactamente una vez despues de completarse todas las iteraciones. La pregunta dice "en secuencia", pero el detalle clave es la ubicacion del Notebook respecto al alcance de ForEach.

**Por que las otras opciones son incorrectas:**
- A) El Notebook se ejecuta una vez solo si esta colocado despues de ForEach, fuera de ese bucle.
- B) El Notebook se ejecuta 100 veces solo si esta dentro del ForEach.
- D) Los Notebooks pueden ir perfectamente despues de activities ForEach en un pipeline; no existe esa restriccion.

</details>

---

### Pregunta 44

Quieres crear un Lakehouse shortcut que apunte a datos en otro Lakehouse dentro del mismo Fabric workspace. Que tipo de shortcut deberias crear?

- A) ADLS Gen2 shortcut
- B) Amazon S3 shortcut
- C) OneLake shortcut
- D) Dataverse shortcut

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) OneLake shortcut**

**Explicacion:** Los OneLake shortcuts te permiten crear referencias a datos en otros items de Fabric (Lakehouses, Warehouses) dentro del mismo o de distintos workspaces. Como el destino es otro Lakehouse dentro del mismo Fabric workspace, un OneLake shortcut es el tipo adecuado. Proporciona acceso zero-copy a los datos sin duplicacion.

**Por que las otras opciones son incorrectas:**
- A) Los ADLS Gen2 shortcuts son para cuentas externas de Azure Data Lake Storage Gen2, no para items internos de Fabric.
- B) Los Amazon S3 shortcuts se conectan a buckets de AWS S3, no a Lakehouses internos de Fabric.
- D) Los Dataverse shortcuts se conectan a tablas de Dataverse, no a Lakehouses de Fabric.

</details>

---

### Pregunta 45

Un control de calidad de datos en la capa Silver revela que el 5% de los registros entrantes tienen valores NULL en una columna requerida `customer_id`. El equipo quiere poner en cuarentena esos registros malos en lugar de descartarlos. Cual es el MEJOR enfoque?

- A) Usar un notebook de Spark para filtrar los registros malos hacia una tabla Delta de cuarentena separada y escribir los registros buenos en la tabla Silver
- B) Permitir todos los registros en la tabla Silver y marcar los malos con una columna
- C) Rechazar todo el lote si algun registro tiene un `customer_id` NULL
- D) Reemplazar los valores NULL por un valor predeterminado como "UNKNOWN"

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Usar un notebook de Spark para filtrar los registros malos hacia una tabla Delta de cuarentena separada y escribir los registros buenos en la tabla Silver**

**Explicacion:** Separar los registros malos en una tabla de cuarentena preserva la calidad de datos en la capa Silver mientras conserva los registros problematicos para investigacion y remediacion. Este patron asegura que la capa Silver contenga solo datos validos mientras mantiene una trazabilidad completa de los registros rechazados. La tabla de cuarentena puede revisarse y los registros corregidos pueden reprocesarse.

**Por que las otras opciones son incorrectas:**
- B) Permitir registros malos en la tabla Silver debilita el proposito de tener una capa de datos limpia y confiable.
- C) Rechazar todo el lote debido a un 5% de registros malos descartaria el 95% de datos validos, lo cual es innecesariamente agresivo.
- D) Reemplazar NULLs con valores por defecto oculta problemas de calidad y puede producir analitica incorrecta downstream.

</details>

---

### Pregunta 46

Necesitas escribir datos desde un notebook de Spark hacia una tabla de Fabric Warehouse. Que metodo se recomienda?

- A) Usar `spark.write.format("delta").save()` apuntando a la ruta del Warehouse
- B) Usar el conector JDBC para escribir directamente al Warehouse
- C) Escribir a una tabla de staging en Lakehouse y usar COPY INTO o INSERT en el Warehouse
- D) Usar `spark.write.format("sqldw")` con una cadena de conexion del Warehouse

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: C) Escribir a una tabla de staging en Lakehouse y usar COPY INTO o INSERT en el Warehouse**

**Explicacion:** Los notebooks de Spark en Fabric no pueden escribir directamente a tablas de Warehouse. El patron recomendado es escribir primero los datos en una tabla Delta de staging en Lakehouse y luego usar T-SQL (COPY INTO o cross-database INSERT INTO ... SELECT) para cargarlos en el Warehouse. Esto aprovecha las fortalezas de cada motor: Spark para transformacion y T-SQL para carga en Warehouse.

**Por que las otras opciones son incorrectas:**
- A) El escritor con formato `delta` apunta al almacenamiento de Lakehouse, no a tablas de Warehouse, que tienen un motor de almacenamiento distinto.
- B) La conectividad JDBC hacia Fabric Warehouse desde notebooks de Spark no es el patron recomendado en Fabric.
- D) El conector `sqldw` esta disenado para Azure Synapse dedicated SQL pools, no para Fabric Warehouse.

</details>

---

### Pregunta 47

Un data engineer ejecuta `DESCRIBE HISTORY` sobre una tabla Delta y ve 500 versiones. Cada version representa una escritura micro-batch por hora. Quiere conservar solo los ultimos 7 dias de historial (168 versiones). Como deberia configurarlo?

- A) Establecer la propiedad de tabla Delta `delta.logRetentionDuration` en `7 days`
- B) Eliminar manualmente entradas antiguas de la carpeta `_delta_log`
- C) Ejecutar VACUUM con una retencion de 168 horas
- D) Recrear la tabla y recargar solo los ultimos 7 dias de datos

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: A) Establecer la propiedad de tabla Delta `delta.logRetentionDuration` en `7 days`**

**Explicacion:** La propiedad de tabla `delta.logRetentionDuration` controla durante cuanto tiempo Delta Lake conserva entradas del transaction log. Configurarla en `7 days` significa que las entradas de log de mas de 7 dias se limpiaran durante operaciones de checkpoint. Combinada con VACUUM (que elimina archivos de datos antiguos), esto controla tanto el historial del log como la huella de almacenamiento.

**Por que las otras opciones son incorrectas:**
- B) Eliminar manualmente elementos de `_delta_log` puede corromper el transaction log de la tabla y romper las garantias de consistencia.
- C) VACUUM elimina archivos de datos antiguos, pero no controla la retencion del transaction log; se necesita `logRetentionDuration` para limpiar el log.
- D) Recrear la tabla es destructivo e innecesario cuando Delta Lake ya ofrece configuracion de retencion integrada.

</details>

---

### Pregunta 48

Tienes una tabla fact grande en un Fabric Lakehouse particionada por `year` y `month`. Un data engineer escribe datos nuevos para enero de 2024. Que particiones se ven afectadas por esta escritura?

- A) Se reescriben todas las particiones
- B) Solo se escribe la particion `year=2024/month=1`
- C) Toda la tabla se bloquea durante la escritura
- D) Se reescriben todas las particiones para `year=2024`

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Solo se escribe la particion `year=2024/month=1`**

**Explicacion:** Delta Lake usa granularidad a nivel de particion para las escrituras. Cuando se escriben datos para enero de 2024, solo se ve afectado el directorio de particion `year=2024/month=1`. Las otras particiones permanecen intactas. Este es uno de los beneficios clave del particionamiento: limita el alcance de las escrituras y permite lecturas concurrentes en particiones no afectadas.

**Por que las otras opciones son incorrectas:**
- A) Delta Lake no reescribe todas las particiones; solo escribe en las particiones que contienen datos nuevos o modificados.
- C) Delta Lake usa concurrencia optimista, no bloqueos de tabla completa; otras particiones pueden leerse y escribirse concurrentemente.
- D) Solo se afecta la particion del mes especifico, no todas las del anio.

</details>

---

### Pregunta 49

Un data engineer necesita programar un Dataflow Gen2 para que se ejecute cada 4 horas. Quiere asegurarse de que las salidas del Dataflow esten disponibles en el Lakehouse para pipelines downstream. Cual es la MEJOR forma de programarlo y orquestarlo?

- A) Usar la programacion de refresh incorporada de Dataflow Gen2 configurada cada 4 horas
- B) Incrustar el Dataflow Gen2 en un Data Factory pipeline con un tumbling window trigger
- C) Usar Power Automate para disparar el Dataflow Gen2 cada 4 horas
- D) Refrescar manualmente el Dataflow Gen2 desde el portal de Fabric

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Incrustar el Dataflow Gen2 en un Data Factory pipeline con un tumbling window trigger**

**Explicacion:** Incrustar el Dataflow Gen2 en un Data Factory pipeline proporciona capacidades de orquestacion que incluyen programacion, gestion de dependencias, manejo de errores e integracion con activities downstream. Un tumbling window trigger ejecuta el pipeline a intervalos fijos de 4 horas y asegura que cada ventana se procese exactamente una vez, por lo que es ideal para programacion regular por intervalos.

**Por que las otras opciones son incorrectas:**
- A) Aunque Dataflow Gen2 admite programaciones de refresh incorporadas, incrustarlo en un pipeline proporciona mejor orquestacion con activities downstream y mejor manejo de errores.
- C) Power Automate agrega una dependencia externa y no se integra tan estrechamente con las capacidades de orquestacion y monitoreo de Fabric.
- D) El refresh manual no es automatizado y no cumple el requisito de ejecucion programada.

</details>

---

### Pregunta 50

Tu equipo esta debatiendo si implementar una transformacion de datos como un Dataflow Gen2 o como un notebook de Spark. La transformacion implica unir dos tablas, aplicar 15 reglas de negocio, manejar slowly changing dimensions y escribir en varias tablas de salida. Que opcion es MEJOR y por que?

- A) Dataflow Gen2, porque proporciona una interfaz visual que los usuarios de negocio pueden mantener
- B) Notebook de Spark, porque proporciona el control programatico total necesario para transformaciones complejas con multiples salidas
- C) Cualquiera de las dos funciona igual de bien para este escenario
- D) Ninguna funciona; esto requiere un stored procedure en el Warehouse

<details>
<summary>Mostrar respuesta</summary>

**Respuesta correcta: B) Notebook de Spark, porque proporciona el control programatico total necesario para transformaciones complejas con multiples salidas**

**Explicacion:** Los requisitos de la transformacion - joins complejos, 15 reglas de negocio, manejo de SCD y multiples tablas de salida - exigen la flexibilidad de un notebook de Spark. Los notebooks proporcionan control programatico completo con PySpark o Scala, admiten logica compleja con ramificacion condicional y funciones personalizadas, y pueden escribir a multiples tablas Delta dentro de una sola ejecucion. Dataflow Gen2 es mas adecuado para transformaciones mas simples con un solo destino.

**Por que las otras opciones son incorrectas:**
- A) Aunque Dataflow Gen2 tiene una interfaz visual, se vuelve inmanejable y dificil de mantener con 15 reglas de negocio, logica SCD y multiples salidas.
- C) Este escenario favorece claramente a los notebooks debido a la complejidad y a los multiples outputs; las dos opciones no son equivalentes aqui.
- D) Los stored procedures en Warehouse pueden manejar transformaciones T-SQL, pero no son la mejor herramienta para manejo de SCD y escrituras a multiples tablas de Lakehouse.

</details>

---
