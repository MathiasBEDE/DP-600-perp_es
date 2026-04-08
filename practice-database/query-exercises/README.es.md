# Ejercicios de consulta

Esta carpeta contiene ejercicios graduados de consultas T-SQL basados en la base de datos de práctica **Contoso Retail Analytics**. Cada ejercicio incluye un enunciado de pregunta y un bloque de solución colapsable.

## Niveles de ejercicios

| Archivo | Nivel | Temas |
|---|---|---|
| [beginner-exercises.md](beginner-exercises.md) | Beginner | `SELECT`, `WHERE`, `ORDER BY`, `GROUP BY`, `JOINs` básicos, funciones de agregación |
| [intermediate-exercises.md](intermediate-exercises.md) | Intermediate | Window functions, CTEs, `HAVING`, subconsultas, `JOINs` de varias tablas, expresiones `CASE` |
| [advanced-exercises.md](advanced-exercises.md) | Advanced | `COPY INTO`, CTAS, consultas cross-database, procedimientos almacenados, optimización de rendimiento, medallion architecture |

## Cómo usarlo

1. Asegúrate de haber creado el esquema y cargado los seed data (consulta el [README padre](../README.es.md)).
2. Empieza con los ejercicios **beginner** y avanza progresivamente.
3. Intenta escribir cada consulta por tu cuenta antes de expandir la solución.
4. Ejecuta tu consulta contra la base de datos de práctica para verificar los resultados.

## Consejos

- Lee cada pregunta cuidadosamente; presta atención al ordenamiento, alias de columnas y condiciones de filtrado.
- Si te bloqueas, revisa el esquema en [`schemas/schema.sql`](../schemas/schema.sql) para entender las relaciones entre tablas.
- Los ejercicios advanced cubren funcionalidades SQL específicas de Fabric que tienen una ponderación importante en el examen DP-600.
