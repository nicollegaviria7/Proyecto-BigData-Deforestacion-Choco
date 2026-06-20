# 🌳 Proyecto Final - Módulo: Big Data
**Especialización en Analítica de Datos**  
**Docente:** Yeis Livis Taborda Henao  
**Periodo Académico:** 1 - 2026  
**Estudiante:** Nicolle Gaviria Mejia

<img width="1024" height="1536" alt="Copilot_20260619_191352" src="https://github.com/user-attachments/assets/7a87f883-b65c-4a61-8f79-df6493dbcce7" />

---

## 💼 1. CASO DE NEGOCIO

### 🚨 Descripción del Problema
El Departamento del Chocó es una de las regiones con mayor biodiversidad y riqueza forestal en el mundo; sin embargo, enfrenta una problemática crítica de pérdida de cobertura vegetal debido a actividades antrópicas y fenómenos climáticos. El análisis y monitoreo manual de las alertas georreferenciadas distribuidas en sus municipios limita la capacidad de respuesta de la máxima autoridad ambiental. 

A partir del conjunto de datos institucionales `AREAS_DEFORESTADAS_CHOCO_20260619.csv`, se identifican **7,937 registros** de áreas degradadas por factores como minería, cultivos e incendios forestales. El desafío principal radica en la falta de un procesamiento masivo y automatizado de datos (Big Data) que impida clasificar el impacto real en hectáreas, segmentar los focos críticos por municipio y desplegar acciones oportunas de educación ambiental y control territorial.

### 🎯 Objetivos del Proyecto
<img width="1408" height="768" alt="Gemini_Generated_Image_w6h7euw6h7euw6h7" src="https://github.com/user-attachments/assets/dd9bb33c-d058-4693-a5ec-43ac9253e0b7" />

---

## 📊 2. RELACIÓN BENEFICIO/COSTE

### 💰 Análisis Económico
*   **🛠️ Proceso Tradicional (Manual):** Actualmente, la auditoría, limpieza cartográfica y análisis de consistencia de los más de 7,900 registros (validando nulos en imágenes satelitales e inconsistencias de formato sexagesimal en coordenadas) requiere de un equipo de 3 especialistas SIG / Ingenieros Forestales dedicando 40 horas mensuales cada uno (120 horas-hombre al mes). Con una tarifa promedio por hora de $40.000 COP, el costo operativo actual asciende a **$4.800.000 COP mensuales ($57.600.000 COP al año)**.
*   **⚡ Proceso Automatizado (Solución Big Data en la Nube):** La automatización de la ingesta con Apache Spark reduce la intervención humana a solo 4 horas mensuales de auditoría y soporte del pipeline por un Ingeniero de Datos ($160.000 COP mensuales).

### 🧮 Retorno de Inversión (ROI) y Ahorros
*   **Inversión Inicial de Implementación:** $15.000.000 COP (Diseño de arquitectura cloud, configuración del pipeline Medallion en Databricks/Colab y dashboard analítico).
*   **Costo de Operación Anual Cloud:** $1.920.000 COP.
*   **Ahorro Operativo Proyectado (Año 1):** $57.600.000 COP (Costo manual) - $1.920.000 COP (Costo Cloud) = **$55.680.000 COP**.
*   **Cálculo del ROI (Primer Año):**

$$\text{ROI} = \frac{\text{Ahorros} - \text{Inversión}}{\text{Inversión}} \times 100 = \frac{55.680.000 - 15.000.000}{15.000.000} \times 100 = 271.2\%$$

El proyecto genera un **retorno del 271.2% en su primer año**, pagándose por completo en el cuarto mes de operación, optimizando además el presupuesto público destinado a la mitigación ambiental del departamento.

---

## 🏗️ 3. ARQUITECTURA PROPUESTA

### 📊 Diagrama de la Solución End-to-End (Estrategia Medallion)
A continuación, se presenta el esquema gráfico de la arquitectura Lakehouse implementada en Databricks para el procesamiento de las alertas ambientales del departamento del Chocó:

<img width="1243" height="506" alt="Arquitectura Medallion" src="https://github.com/user-attachments/assets/27014e75-dbbb-42dc-9f91-b54196307783" />

---

### ⚙️ Explicación Técnica de los Componentes y Flujo de Información

La arquitectura propuesta se fundamenta en el paradigma moderno de **Lakehouse**, el cual unifica las capacidades de almacenamiento masivo de un *Data Lake* con las características de transaccionalidad, gobierno y consistencia de un *Data Warehouse* tradicional. Esto se logra mediante el uso del formato de almacenamiento abierto **Delta Lake** y el motor de procesamiento distribuido **Apache Spark**.

#### 1. Componentes del Ecosistema Tecnológico:
* **Fuente de Datos e Ingesta:** El archivo indexado `AREAS_DEFORESTADAS_CHOCO_20260619.csv` se establece como la fuente única de verdad. Es almacenado de forma persistente en el sistema de archivos distribuido de Databricks (DBFS) para garantizar un acceso de lectura de baja latencia por parte de los nodos de cómputo.
* **Motor de Procesamiento Central (Databricks - Apache Spark):** Es el núcleo de la arquitectura de Big Data. Utiliza clústeres de cómputo que distribuyen la carga de trabajo en múltiples hilos de ejecución en paralelo. Mediante la API de **PySpark**, ejecuta transformaciones masivas sobre los datos sin importar que el volumen crezca a millones de registros satelitales en el futuro.
* **Entorno de Analítica y Modelado (Google Colab):** Actúa como un entorno satélite para el consumo ágil de datos consolidados. Permite la manipulación avanzada con librerías estadísticas especializadas (Pandas, Seaborn, Matplotlib) y la generación de reportes visuales de alta resolución sin penalizar los recursos del clúster de producción.

#### 2. Flujo de Información y Transformación (Arquitectura Medallion):

* **Capa Bronze (Datos Crudos / Ingesta):** En esta fase, los datos del archivo CSV de deforestación se leen y se almacenan directamente en tablas Delta sin aplicar ninguna regla de negocio o transformación estructural. El objetivo principal es preservar la fidelidad absoluta del histórico con todas sus inconsistencias, nulos y textos crudos para auditorías de datos futuras.
* **Capa Silver (Calidad, Limpieza y Enriquecimiento):** El pipeline programado consume las tablas Bronze y ejecuta procesos de normalización. Utilizando funciones nativas de Spark, se eliminan los puntos de miles y se reemplazan las comas por puntos en la columna `AREA_Ha`, casteándola a un tipo de dato decimal numérico (`Double`). Asimismo, se limpian los valores nulos en el campo `CAUSA`, se unifican las cadenas de texto de los municipios para evitar duplicidades por tildes o espacios, y se reparan los registros de la columna `AÑO` que presentaban formatos decimales erróneos (transformándolos a enteros válidos).
* **Capa Gold (Datos de Negocio / Agregación):** Los datos limpios de la capa Silver se someten a consultas de agregación (operaciones de agrupación y suma indexadas). Aquí se estructuran tablas analíticas optimizadas que pre-calculan las hectáreas deforestadas totales y el conteo de alertas específicas por cada Municipio y Año. 
* **Capa de Consumo Final:** Las herramientas de visualización y el cuaderno científico de Google Colab consumen directamente las tablas Gold ya procesadas. Esto evita tener que recalcular las transformaciones matemáticas sobre las 7,937 filas originales en cada consulta, optimizando drásticamente el consumo de memoria y la velocidad de respuesta del sistema analítico regional para la toma de decisiones.

---

### 📊 3.1. RESULTADOS ANALÍTICOS (CONSUMO DE LA CAPA GOLD)

Como evidencia de la correcta implementación del pipeline y la disponibilidad de los datos en la **Capa Gold**, se conectó el entorno de **Google Colab** para generar la analítica descriptiva que guiará la toma de decisiones de la entidad ambiental. 

Para asegurar la disponibilidad y evitar pérdidas de datos locales, el reporte se genera consumiendo de forma directa y remota el dataset alojado en este repositorio.

#### 📈 Gráficos de Impacto Territorial y Causales

A continuación, se presentan las visualizaciones de alta resolución obtenidas tras el procesamiento distribuido:

| 📋 Gráfico A: Superficie Deforestada por Causa | 📍 Gráfico B: Top 5 Municipios Afectados |
<img width="4752" height="1752" alt="GRAFICOS" src="https://github.com/user-attachments/assets/2a0814c5-ba89-4225-bb19-40bdf4195b77" />

---

#### 📝 Explicación y Hallazgos de Negocio para la Entidad Ambiental

1. **Análisis del Impacto por Causa Principal (Gráfico Izquierdo):**
   * **Hallazgo:** Aunque comúnmente se asocia la pérdida de bosque exclusivamente a los cultivos agrícolas directos, los datos analizados demuestran que los **Incendios** representan la mayor catástrofe en extensión territorial en el departamento del Chocó, superando las **25,000 hectáreas** destruidas de forma acumulada. La **Minería** se ubica como la segunda causa más crítica, rozando las **19,000 hectáreas**.
   * **Acción Estratégica:** La corporación autónoma regional debe priorizar la asignación presupuestal hacia brigadas de prevención y mitigación de incendios forestales y control de minería ilegal, ya que allí se concentra el mayor impacto severo de pérdida de cobertura vegetal.

2. **Análisis de Priorización Territorial - Top 5 Municipios (Gráfico Derecho):**
   * **Hallazgo:** El análisis revela una concentración crítica del riesgo ambiental en zonas geográficas específicas. El municipio de **RIOSUCIO** lidera la alerta con una diferencia abrumadora, registrando más de **22,000 hectáreas** deforestadas. En segundo lugar se ubica el municipio de **UNGUÍA** con un aproximado de **12,500 hectáreas**.
   * **Acción Estratégica:** Este comportamiento georreferenciado le permite a la entidad aplicar una estrategia de optimización de recursos. En lugar de dispersar el personal operativo en todo el departamento, se deben concentrar los esfuerzos de monitoreo satelital avanzado y comisiones de campo de forma inmediata en Riosucio y Unguía para contener los focos activos de deforestación.
