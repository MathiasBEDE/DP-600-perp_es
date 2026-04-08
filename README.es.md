# 📘 DP-600: Implementación de soluciones analíticas con Microsoft Fabric

> **Microsoft Certified: Fabric Analytics Engineer Associate**
>
> Un repositorio de estudio completo y autocontenido para el examen de certificación **DP-600**, que cubre los cuatro dominios, más de 200 preguntas de práctica, estudios de caso del mundo real, exámenes simulados y un plan de estudio estructurado de 6 semanas.

> **Aviso de traducción (ES):** Esta versión en español fue preparada para hablantes hispanos. La versión original en inglés sigue siendo la fuente principal de referencia. Repositorio fuente original: `guptadeepak18/DP-600-perp`.
>
> **Translation notice (EN):** This Spanish version was prepared for Spanish-speaking readers. The original English version remains the primary reference source. Original source repository: `guptadeepak18/DP-600-perp`.

---

## 📖 Acerca de este repositorio

Este repositorio es un kit integral de preparación para el examen de certificación **DP-600: Implementing Analytics Solutions Using Microsoft Fabric**. Ya seas un data engineer, analytics engineer o profesional de BI que busca validar sus habilidades en Microsoft Fabric, aquí encontrarás todo lo necesario: guías de estudio, preguntas de práctica, ejercicios prácticos y exámenes simulados completos, organizados para llevarte desde los fundamentos hasta estar listo para el examen en seis semanas.

---

## 🎯 Resumen del examen

| Detalle | Descripción |
|---|---|
| **Código del examen** | DP-600 |
| **Nombre del examen** | Implementing Analytics Solutions Using Microsoft Fabric |
| **Certificación** | Microsoft Certified: Fabric Analytics Engineer Associate |
| **Número de preguntas** | 40–60 |
| **Duración** | 120 minutos |
| **Puntaje de aprobación** | 700 / 1000 |
| **Formato** | Opción múltiple, estudios de caso, arrastrar y soltar, basado en escenarios |

### Dominios del examen y ponderaciones

| Dominio | Tema | Peso |
|:---:|---|:---:|
| 1 | Planear, implementar y administrar una solución de analítica de datos | 10–15% |
| 2 | Preparar y servir datos | 40–45% |
| 3 | Implementar y administrar semantic models | 20–25% |
| 4 | Explorar y analizar datos | 20–25% |

> **Idea clave:** El Dominio 2 (Preparar y servir datos) representa casi la mitad del examen. Prioriza Lakehouses, Warehouses, Dataflows y Pipelines.

---

## 📁 Estructura del repositorio

```text
DP-600-perp/
│
├── study-guides/                  # Guías de estudio por dominio
│   ├── 01-plan-implement-manage-analytics.md
│   ├── 01-plan-implement-manage-analytics.es.md
│   ├── 02-prepare-and-serve-data.md
│   ├── 02-prepare-and-serve-data.es.md
│   ├── 03-implement-manage-semantic-models.md
│   └── 04-explore-and-analyze-data.md
│
├── practice-questions/            # Más de 200 preguntas de opción múltiple
│   ├── domain-1-questions.md
│   ├── domain-2-questions.md
│   ├── domain-3-questions.md
│   └── domain-4-questions.md
│
├── case-studies/                  # 5 estudios de caso basados en escenarios reales
│   ├── case-study-1-retail-analytics.md
│   ├── case-study-1-retail-analytics.es.md
│   ├── case-study-2-healthcare-reporting.md
│   ├── case-study-3-financial-data-platform.md
│   ├── case-study-4-manufacturing-iot.md
│   └── case-study-5-education-analytics.md
│
├── practice-database/             # Práctica práctica de SQL
│   ├── schemas/
│   ├── seed-data/
│   └── query-exercises/
│
├── cheat-sheets/                  # Guías de referencia rápida
│   ├── dax-cheat-sheet.md
│   ├── t-sql-cheat-sheet.md
│   ├── fabric-components-cheat-sheet.md
│   └── exam-tips-cheat-sheet.md
│
├── mock-exams/                    # Exámenes simulados completos
│   ├── mock-exam-1.md             # 50 preguntas
│   └── mock-exam-2.md             # 50 preguntas
│
└── README.md
```

### Descripción de carpetas

| Carpeta | Contenido | Propósito |
|---|---|---|
| `study-guides/` | 4 guías por dominio | Notas detalladas alineadas con cada dominio del examen |
| `practice-questions/` | Más de 200 MCQ en 4 archivos | Evalúa tus conocimientos dominio por dominio |
| `case-studies/` | 5 estudios de caso del mundo real | Practica razonamiento basado en escenarios |
| `practice-database/` | Esquemas SQL, seed data, ejercicios de consulta | Práctica práctica de T-SQL y warehouse |
| `cheat-sheets/` | DAX, T-SQL, componentes de Fabric, tips de examen | Revisión de último momento y referencia rápida |
| `mock-exams/` | 2 exámenes simulados completos (50 preguntas cada uno) | Simula condiciones reales de examen |

---

## 📅 Plan de estudio de 6 semanas

### Semana 1 — Fundamentos + Dominio 1: Planear, implementar y administrar

*Objetivo: Comprender el ecosistema de Fabric y la planificación de soluciones.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Leer la guía de estudio del Dominio 1 | [`study-guides/01-plan-implement-manage-analytics.es.md`](study-guides/01-plan-implement-manage-analytics.es.md) |
| Martes | Explorar un workspace de Microsoft Fabric en un tenant de prueba | [Microsoft Fabric Free Trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial) |
| Miércoles | Practicar preguntas del Dominio 1 (primera mitad) | `practice-questions/domain-1-questions.md` |
| Jueves | Practicar preguntas del Dominio 1 (segunda mitad) | `practice-questions/domain-1-questions.md` |
| Viernes | Revisar la cheat sheet de componentes de Fabric | `cheat-sheets/fabric-components-cheat-sheet.md` |
| Sábado | Caso de estudio 1: Retail Analytics | [`case-studies/case-study-1-retail-analytics.es.md`](case-studies/case-study-1-retail-analytics.es.md) |
| Domingo | Revisar y cerrar brechas de conocimiento | Revisa cualquier área débil |

### Semana 2 — Dominio 2 Parte 1: Ingesta de datos, Dataflows y Pipelines

*Objetivo: Dominar el movimiento de datos: Dataflows Gen2, Data Pipelines y conectores.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Leer la guía de estudio del Dominio 2 (primera mitad: ingesta y Dataflows) | [`study-guides/02-prepare-and-serve-data.es.md`](study-guides/02-prepare-and-serve-data.es.md) |
| Martes | Práctica: Crear un Dataflow Gen2 en un workspace de Fabric | Entorno de prueba de Microsoft Fabric |
| Miércoles | Práctica: Construir un Data Pipeline con actividad de copia | Entorno de prueba de Microsoft Fabric |
| Jueves | Practicar preguntas del Dominio 2 (preguntas 1–25) | `practice-questions/domain-2-questions.md` |
| Viernes | Revisar la cheat sheet de T-SQL | `cheat-sheets/t-sql-cheat-sheet.md` |
| Sábado | Caso de estudio 2: Healthcare Reporting | `case-studies/case-study-2-healthcare-reporting.md` |
| Domingo | Revisar y practicar áreas débiles | Revisa notas y preguntas |

### Semana 3 — Dominio 2 Parte 2: Lakehouse, Warehouse y arquitectura Medallion

*Objetivo: Profundizar en patrones de almacenamiento: Lakehouse, Warehouse, Delta Lake y capas medallion.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Leer la guía de estudio del Dominio 2 (segunda mitad: Lakehouse y Warehouse) | [`study-guides/02-prepare-and-serve-data.es.md`](study-guides/02-prepare-and-serve-data.es.md) |
| Martes | Práctica: Crear un Lakehouse y cargar datos con notebooks | Entorno de prueba de Microsoft Fabric |
| Miércoles | Práctica: Crear un Warehouse y escribir consultas T-SQL | Ejercicios de `practice-database/` |
| Jueves | Practicar preguntas del Dominio 2 (preguntas 26–50+) | `practice-questions/domain-2-questions.md` |
| Viernes | Estudiar arquitectura medallion (bronze → silver → gold) | Guía de estudio + Microsoft Learn |
| Sábado | Caso de estudio 3: Financial Data Platform | `case-studies/case-study-3-financial-data-platform.md` |
| Domingo | Revisión completa del Dominio 2 — esto representa 40–45% del examen | Todos los recursos del Dominio 2 |

### Semana 4 — Dominio 3: Implementar y administrar semantic models

*Objetivo: Dominar semantic models, DAX, relaciones y seguridad a nivel de fila.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Leer la guía de estudio del Dominio 3 | `study-guides/03-implement-manage-semantic-models.md` |
| Martes | Profundizar en patrones DAX (`CALCULATE`, transición de contexto) | `cheat-sheets/dax-cheat-sheet.md` |
| Miércoles | Práctica: Construir un semantic model con relaciones y medidas | Entorno de prueba de Microsoft Fabric |
| Jueves | Practicar preguntas del Dominio 3 (primera mitad) | `practice-questions/domain-3-questions.md` |
| Viernes | Practicar preguntas del Dominio 3 (segunda mitad) + práctica de RLS | `practice-questions/domain-3-questions.md` |
| Sábado | Caso de estudio 4: Manufacturing IoT | `case-studies/case-study-4-manufacturing-iot.md` |
| Domingo | Revisar cheat sheet de DAX y áreas débiles | Cheat sheets + notas |

### Semana 5 — Dominio 4: Explorar y analizar datos

*Objetivo: Cubrir reporting, analítica avanzada, CI/CD y deployment pipelines.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Leer la guía de estudio del Dominio 4 | `study-guides/04-explore-and-analyze-data.md` |
| Martes | Práctica: Crear reports y dashboards en Fabric | Entorno de prueba de Microsoft Fabric |
| Miércoles | Estudiar CI/CD, deployment pipelines y ALM en Fabric | Guía de estudio + Microsoft Learn |
| Jueves | Practicar preguntas del Dominio 4 (primera mitad) | `practice-questions/domain-4-questions.md` |
| Viernes | Practicar preguntas del Dominio 4 (segunda mitad) | `practice-questions/domain-4-questions.md` |
| Sábado | Caso de estudio 5: Education Analytics | `case-studies/case-study-5-education-analytics.md` |
| Domingo | Revisar las cuatro cheat sheets de dominio | `cheat-sheets/` |

### Semana 6 — Revisión, exámenes simulados y preparación final

*Objetivo: Consolidar conocimiento, simular condiciones de examen y ganar confianza.*

| Día | Actividad | Recursos |
|---|---|---|
| Lunes | Tomar el examen simulado 1 (cronometrado: 100 minutos para 50 preguntas) | `mock-exams/mock-exam-1.md` |
| Martes | Revisar el examen simulado 1: estudia a fondo cada respuesta incorrecta | Explicaciones de respuestas del examen simulado 1 |
| Miércoles | Revisión profunda del dominio o dominios más débiles | Guías de estudio + preguntas de práctica |
| Jueves | Tomar el examen simulado 2 (cronometrado: 100 minutos para 50 preguntas) | `mock-exams/mock-exam-2.md` |
| Viernes | Revisar el examen simulado 2: enfócate en patrones de error | Explicaciones de respuestas del examen simulado 2 |
| Sábado | Revisión final: todas las cheat sheets + tips de examen | `cheat-sheets/exam-tips-cheat-sheet.md` |
| Domingo | Revisión ligera solamente: descansa y prepárate para el día del examen | Relájate y mantén la calma |

---

## 🚀 Cómo usar este repositorio

1. **Clona el repositorio**
   ```bash
   git clone https://github.com/guptadeepak18/DP-600-perp.git
   cd DP-600-perp
   ```

2. **Sigue el plan de estudio de 6 semanas** anterior, o adáptalo a tu agenda.

3. **Lee la guía de estudio** de cada dominio antes de intentar responder preguntas de práctica.

4. **Resuelve preguntas de práctica** dominio por dominio. Revisa las explicaciones de cada pregunta, incluso de las que respondas bien.

5. **Completa estudios de caso** para desarrollar habilidades de razonamiento basado en escenarios, que son críticas para el examen.

6. **Usa la practice database** para ganar experiencia práctica escribiendo consultas T-SQL sobre esquemas realistas.

7. **Revisa cheat sheets** con frecuencia, especialmente DAX y T-SQL, como refuerzo de referencia rápida.

8. **Haz exámenes simulados en condiciones cronometradas** en la Semana 6 para simular la experiencia real del examen.

9. **Haz seguimiento de tu progreso** usando la siguiente lista:

   - [x] Guía de estudio del Dominio 1 completada
   - [x] Preguntas de práctica del Dominio 1 completadas
   - [ ] Guía de estudio del Dominio 2 completada
   - [ ] Preguntas de práctica del Dominio 2 completadas
   - [ ] Guía de estudio del Dominio 3 completada
   - [ ] Preguntas de práctica del Dominio 3 completadas
   - [ ] Guía de estudio del Dominio 4 completada
   - [ ] Preguntas de práctica del Dominio 4 completadas
   - [ ] Los 5 estudios de caso completados
   - [ ] Examen simulado 1 completado (puntaje: ___%)
   - [ ] Examen simulado 2 completado (puntaje: ___%)
   - [ ] Todas las cheat sheets revisadas
   - [ ] Examen programado 🎯

---

## 💡 Tips y estrategias para el examen

1. **Domina primero el Dominio 2.** Representa el 40–45% del examen. No aprobarás sin una base sólida en Lakehouses, Warehouses, Dataflows y Pipelines.

2. **Saber cuándo usar cada cosa.** El examen con frecuencia pide elegir entre Lakehouse vs. Warehouse, Dataflow Gen2 vs. Data Pipeline, o DirectLake vs. modo Import. Comprende los trade-offs.

3. **Aprende la transición de contexto en DAX.** Espera preguntas sobre `CALCULATE`, `FILTER`, `ALL`, `REMOVEFILTERS` y cómo el contexto de filtro se propaga a través de las relaciones.

4. **Comprende la arquitectura medallion.** Conoce el propósito de las capas bronze (raw), silver (cleansed) y gold (aggregated), y cómo se asignan a componentes de Fabric.

5. **Lee cada palabra de la pregunta.** Las preguntas basadas en escenarios suelen contener requisitos sutiles en una sola oración que cambian completamente la respuesta correcta.

6. **Administra tu tiempo.** Con 40–60 preguntas en 120 minutos, tienes aproximadamente 2–3 minutos por pregunta. Marca las preguntas difíciles y vuelve luego.

7. **Elimina respuestas claramente incorrectas.** La mayoría de las preguntas tienen al menos una respuesta obviamente incorrecta. Reduce opciones antes de elegir.

8. **Conoce row-level security (RLS) y object-level security (OLS).** Espera al menos algunas preguntas sobre configuración de seguridad en semantic models.

9. **Comprende deployment pipelines y CI/CD.** Debes saber cómo interactúan los workspaces de Fabric con las etapas de desarrollo, prueba y producción.

10. **Practica T-SQL para Fabric Warehouses.** El examen evalúa habilidades prácticas de T-SQL: `COPY INTO`, `CREATE TABLE AS SELECT`, consultas cross-database y procedimientos almacenados.

11. **No sobrepienses.** Si redujiste la respuesta a dos opciones, elige la que se alinee con las prácticas recomendadas por Microsoft.

12. **Revisa el skills outline oficial** antes de la fecha del examen para asegurarte de que no se hayan agregado temas nuevos. Microsoft actualiza periódicamente el contenido del examen.

---

## 🔗 Recursos de Microsoft Learn

### Dominio 1: Planear, implementar y administrar una solución de analítica de datos
- [Get started with Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/get-started-fabric/)
- [Administer Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/administer-microsoft-fabric/)

### Dominio 2: Preparar y servir datos
- [Ingest data with Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/ingest-data-with-microsoft-fabric/)
- [Implement a lakehouse with Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/implement-lakehouse-microsoft-fabric/)
- [Implement a data warehouse with Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/work-with-data-warehouses-using-microsoft-fabric/)

### Dominio 3: Implementar y administrar semantic models
- [Implement and manage semantic models](https://learn.microsoft.com/en-us/training/paths/implement-manage-semantic-models/)
- [DAX in Power BI](https://learn.microsoft.com/en-us/training/paths/dax-power-bi/)

### Dominio 4: Explorar y analizar datos
- [Explore and analyze data with Microsoft Fabric](https://learn.microsoft.com/en-us/training/paths/explore-analyze-data-fabric/)

### Recursos adicionales
- [Official DP-600 Exam Page](https://learn.microsoft.com/en-us/credentials/certifications/exams/dp-600/)
- [DP-600 Skills Measured (PDF)](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600)
- [Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)
- [Microsoft Fabric Free Trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)

---

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Si quieres mejorar este recurso de estudio:

1. **Haz un fork** de este repositorio.
2. **Crea una branch** para tus cambios: `git checkout -b feature/your-improvement`.
3. **Haz commit** de tus cambios con un mensaje claro.
4. **Abre un Pull Request** describiendo lo que agregaste o corregiste.

Las contribuciones pueden incluir:
- Nuevas preguntas de práctica con explicaciones detalladas
- Correcciones o actualizaciones del contenido existente
- Estudios de caso adicionales o escenarios del mundo real
- Mejoras en cheat sheets o guías de estudio
- Traducciones a otros idiomas

Por favor, asegúrate de que todas las preguntas de práctica incluyan explicaciones de respuesta y hagan referencia al dominio del examen correspondiente.

---

## 📄 Licencia

Este proyecto está licenciado bajo la **MIT License**.

```text
MIT License

Copyright (c) 2024

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

<p align="center">
  <strong>¡Éxitos en tu examen DP-600! 🎓</strong><br>
  <em>Si este repositorio te ayuda a aprobar, considera darle una ⭐</em>
</p>
