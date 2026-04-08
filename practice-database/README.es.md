# Base de datos de práctica para preparación del examen DP-600

Este directorio contiene un entorno práctico de SQL diseñado para el examen de certificación **DP-600: Implementing Analytics Solutions Using Microsoft Fabric**. Proporciona un modelo dimensional realista de analítica retail/ventas, datos de ejemplo y ejercicios de consulta graduados.

## Resumen

La base de datos de práctica modela una empresa retail ficticia, **Contoso Retail Analytics**, con un modelo dimensional en star schema. Cubre los tipos de patrones de data warehousing que encontrarás en el examen DP-600, incluidos:

- Modelado dimensional (`star schema` con tablas de hechos y dimensiones)
- Consultas T-SQL en Microsoft Fabric Warehouse
- Agregación, window functions y consultas analíticas
- Funcionalidades SQL específicas de Fabric (`COPY INTO`, CTAS, consultas cross-database)

## Tabla de contenido

| Carpeta | Descripción |
|---|---|
| [`schemas/`](schemas/) | Definiciones de tablas (DDL) para el modelo dimensional |
| [`seed-data/`](seed-data/) | Sentencias `INSERT` para poblar las tablas con datos de ejemplo |
| [`query-exercises/`](query-exercises/) | Ejercicios T-SQL beginner, intermediate y advanced con soluciones |

## Cómo usarlo

### Opción 1: Microsoft Fabric Warehouse (recomendada)

1. Abre tu **Microsoft Fabric workspace** y crea un nuevo **Warehouse**.
2. Abre el editor de consultas SQL.
3. Ejecuta [`schemas/schema.sql`](schemas/schema.sql) para crear todas las tablas.
4. Ejecuta [`seed-data/seed-data.sql`](seed-data/seed-data.sql) para poblar las tablas con datos de ejemplo.
5. Completa los ejercicios en [`query-exercises/`](query-exercises/).

### Opción 2: SQL Server / Azure SQL Database

El esquema y los seed data usan sintaxis T-SQL estándar y son compatibles con:

- SQL Server 2019+
- Azure SQL Database
- Azure Synapse Analytics (dedicated SQL pool)

Ejecuta los scripts de esquema y seed data en tu cliente SQL preferido (SSMS, Azure Data Studio, etc.).

### Opción 3: sqlcmd o CLI

```bash
# Conectarse a tu Fabric Warehouse o instancia de SQL Server
sqlcmd -S <server> -d <database> -U <user> -P <password> -i schemas/schema.sql
sqlcmd -S <server> -d <database> -U <user> -P <password> -i seed-data/seed-data.sql
```

## Diagrama del esquema

```
                    ┌──────────────┐
                    │  DimCustomer │
                    └──────┬───────┘
                           │
┌────────────┐    ┌────────┴────────┐    ┌────────────┐
│  DimStore  ├────┤    FactSales    ├────┤ DimProduct  │
└────────────┘    └────────┬────────┘    └────────────┘
                           │
                    ┌──────┴───────┐
                    │   DimDate    │
                    └──────┬───────┘
                           │
┌──────────────┐   ┌───────┴────────┐
│ DimPromotion ├───┤ FactInventory  │
└──────────────┘   └────────────────┘
```

## Consejos para preparación del examen

- Presta atención a los **tipos de datos soportados por Fabric**; Fabric Warehouse soporta un subconjunto de tipos T-SQL.
- Practica escribir consultas con **window functions** (`ROW_NUMBER`, `RANK`, `LAG`, `LEAD`); se evalúan con frecuencia.
- Comprende la **medallion architecture** (Bronze → Silver → Gold) para patrones de transformación de datos.
- Aprende a usar **COPY INTO** para ingesta de datos y **CREATE TABLE AS SELECT (CTAS)** para transformación de datos.

## Prerrequisitos

- Familiaridad básica con T-SQL / SQL
- Acceso a un Microsoft Fabric Workspace (hay trial gratuito disponible) o a una instancia de SQL Server
- Un cliente SQL (editor web de Fabric, SSMS o Azure Data Studio)
