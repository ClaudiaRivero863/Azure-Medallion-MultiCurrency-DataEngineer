# Motor de datos multidivisa Azure Medallion

Pipeline de datos automatizado y escalable de punta a punta (*end-to-end*) desarrollado en **Microsoft Azure y Databricks**, implementando la **Arquitectura Medallion** (Bronze, Silver, Gold) para la unificación y análisis financiero de transacciones en múltiples monedas.

---

## 📌 Contexto y Problemática del Negocio

Una compañía comercial que opera a nivel internacional procesa ventas diarias registradas en diversas monedas locales (ARS, BRL, MXN, EUR, etc.). La alta gerencia enfrentaba demoras y falta de estandarización al consolidar los informes financieros.

**Solución Implementada:** Se diseñó una arquitectura en la nube para automatizar la ingesta de transacciones comerciales y cotizaciones de divisas mediante una API REST externa, aplicando conversiones automáticas al dólar estadounidense (*USD*) y controles de calidad para entregar una fuente de verdad confiable (*Single Source of Truth*).

---

## 🏗️ Arquitectura del Proyecto (Estructura de Directorios en Azure Data Lake)

El flujo de datos se procesa a través de tres capas principales en **Azure Data Lake Storage Gen2** (`sttpiicaro`):
1. **Capa Bronze (Raw):** Almacenamiento inmutable de los archivos CSV transaccionales y las respuestas JSON de la API REST de tipos de cambio.
2. **Capa Silver (Clean & Standardized):** Limpieza de datos, eliminación de duplicados, validaciones de calidad (*Data Quality Checks*) y cruce (*Join*) con las cotizaciones diarias para unificar montos a $USD$.
3. **Capa Gold (Business / Aggregations):** Modelado de la tabla de hechos (`fact_ventas`) y generación de la vista agregada analítica (`kpi_ventas_gerencial`) en Delta Lake.

---

## 🛠️ Stack Tecnológico
* **Cloud Platform:** Microsoft Azure
* **Storage:** Azure Data Lake Storage Gen2 (`sttpiicaro` / contenedor `tpi-data`)
* **Orquestación:** Azure Data Factory (ADF) con desencadenador diario (`trg_daily_medallion_pipeline`)
* **Procesamiento de Datos:** Databricks, PySpark y Delta Lake
* **Lenguajes:** Python y SQL

```text
📁 sttpiicaro (Storage Account)
└── 📁 tpi-data (Contenedor principal)
    ├── 📁 bronze/                      # Capa Raw / Inmutabilidad
    │   ├── 📁 ventas_raw/              # CSVs sintéticos transaccionales ingestados por ADF
    │   └── 📁 api_cotizaciones_raw/      # Respuestas JSON originales de la API REST de divisas
    │
    ├── 📁 silver/                      # Capa de Limpieza y Estandarización
    │   ├── 📁 ventas_clean/            # Datos limpios, filtrados y sin duplicados (Delta Lake)
    │   └── 📁 cotizaciones_clean/      # Cotizaciones diarias procesadas (Delta Lake)
    │
    └── 📁 gold/                        # Capa Analítica / Modelo de Negocio
        ├── 📁 fact_ventas/             # Tabla de hechos con montos unificados a USD (Delta Lake)
        └── 📁 kpi_ventas_gerencial/    # Datasets agregados listos para consumo analítico


---
⚙️ Automatización (Orquestación)
El flujo completo se ejecuta mediante un pipeline en Azure Data Factory (pipeline_medallion_tpi), configurado con un desencadenador diario (trg_daily_medallion_pipeline). Este orquestador secuencia la ingesta cruda y dispara de forma automatizada los notebooks de transformación en los clústeres distribuidos de Databricks.


---
👩‍💻 Autor
Claudia Rivero — Data Engineer

Entorno: Diplomatura en Data Engineering (Ícaro - UNC)
