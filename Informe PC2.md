# Informe Final de Analítica Avanzada y Big Data – NeoCab NYC (PC2)
<img width="1200" height="800" alt="image" src="https://github.com/user-attachments/assets/fa3f77c8-7dc2-4df4-a8dc-40752ea8107d" />

**Curso:** AD3005 – Introducción a Data Analytics y Big Data (UTEC)  
**Caso 2:** Movilidad urbana y demanda de transporte  
**Grupo:** 1  

---

## 📂 Archivos Adjuntos y Enlaces del Proyecto

Para la revisión de los entregables técnicos y código fuente de esta investigación, puede acceder directamente a los recursos haciendo clic en los siguientes enlaces:

* 📊 **Dashboard Interactivo de Negocio:** [DashboardNeoCab.html](DashboardNeoCab.html) | Archivo web autoejecutable que contiene las visualizaciones avanzadas de BI, KPIs operacionales y la distribución geoespacial de la flota en NYC.
* 🚀 **Notebook de Procesamiento y Modelado en Big Data (Databricks):** [TrabajoFINAL_NYC_grupo_1.ipynb]
* (TrabajoFINAL_NYC_grupo_1.ipynb) | Código fuente desarrollado en el entorno de clúster de Databricks que integra el pipeline de ingeniería de datos (PySpark), las agregaciones analíticas de los 45 millones de registros y el entrenamiento del modelo predictivo (scikit-learn).
* [Presentación: PC2_NeoCab Grupo 1.pdf](https://github.com/isabellamcb/Big-Data-Project/blob/c4018b0cf38dd444f8577007117c119d4b4d7616/PC2_NeoCab%20Grupo%201.pdf)
* https://canva.link/dhrh7h7frm6yhll 

---

## 1. Portada del Equipo y Evolución de Roles (Fase 2)

* **Bravo Chávez, Javier Alonso:** *Responsable de Estrategia Comercial* | Análisis económico de los escenarios cuantitativos y diseño técnico de los indicadores de éxito (KPIs).
* **Castillo Braschi, Isabella María:** *Directora de Gestión de Proyecto (PM)* | Coordinación ágil del equipo y responsable de estructurar la unión, limpieza y cruce de las bases de datos masivas en el clúster.
* **Corzo Quispe, Ernesto Rodrigo:** *Científico de Datos (Data Scientist)* | Responsable de la analítica predictiva, desarrollo, entrenamiento y evaluación de precisión del modelo de Machine Learning.
* **Godoy Castillo, Diego Alonso:** *Ingeniero de Calidad de Datos (QA)* | Responsable del control de calidad del pipeline, corrección de valores nulos, baches lógicos y auditoría de gobierno de herramientas de IA.
* **Martell Lino, Giancarlos Arcadio:** *Especialista en Visualización (BI)* | Diseñador y desarrollador del Dashboard final, asegurando la traducción de datos crudos a insights de negocio.

---

## 2. Descripción del Problema de Negocio (Relevancia de la Fase 2)

**Empresa afectada:** Startup de movilidad urbana enfocada en transformar el transporte tecnológico en la ciudad de Nueva York.

Mientras que en la Fase 1 el problema se limitó a la observación empírica de fluctuaciones de viajes, en la **Fase 2 el desafío escaló a la optimización crítica de la rentabilidad del activo**. La asimetría en la distribución geográfica de la flota provoca una pérdida masiva de viajes y deserción de conductores (*churn*) en condiciones climáticas adversas y zonas periféricas por falta de incentivos económicos automáticos. 

Para resolver esto, NeoCab requiere abandonar los esquemas de precios estáticos tradicionales e implementar un sistema distribuido de **fijación dinámica de precios (Surge Pricing) e inteligencia predictiva de despacho vial**, permitiendo mitigar el desabastecimiento crónico de unidades.

---

## 3. Descripción de los Datos Consolidados

Tras el proceso de ingeniería de datos en la plataforma distribuida Databricks, las especificaciones de nuestra base de datos maestro son:

* **Fuente Principal:** Registros históricos de viajes de vehículos de alta densidad (*High-Volume For-Hire Vehicle Trip Records* - Uber/Lyft) en NYC. Volumen unificado de **45,931,107 registros** mediante combinación vertical de los meses de agosto, septiembre y octubre del 2021.
* **Fuentes de Enriquecimiento (Cruce de Datos):** 1. Base de datos climatológica por hora (temperatura, precipitación, tipo de condiciones).
    2. Diccionario espacial oficial de zonas de taxis (mapeo de IDs de zonas a distritos o *Boroughs*).
    3. Calendario oficial de feriados en EE.UU.
    4. Capa geoespacial de *Hotspots de Vida Nocturna* (zonas de alta concentración de ocio nocturno).
* **Variables Clave del Dataset Maestro (`df_estudio`):** `base_passenger_fare` (Target numérico), `trip_miles`, `trip_time`, `temp` (Double), `precip` (Double), `conditions`, `Borough`, `es_feriado` y `is_nightlife_hotspot`.

---

## 4. Matriz de Evolución y Mejoras (PC1 vs PC2)

La siguiente bitácora de auditoría de código evidencia el salto cualitativo y técnico entre ambas entregas:

| Componente | Estado en la Entrega Pasada (PC1) | Cambios y Modificaciones en la PC2 | Nuevas Adiciones (Exclusivo PC2) |
| :--- | :--- | :--- | :--- |
| **Infraestructura de Datos** | Carga aislada en Pandas/Spark de meses individuales (Agosto, Septiembre por separado). Celdas redundantes de verificación. | Unificación vertical de **45,931,107 de filas** mediante `.unionByName()`. Corrección de baches lógicos de fechas convirtiendo cadenas a tipo fecha real con `.to_date()`. | **Persistencia Avanzada:** Implementación de `.cache()` estratégico sobre el dataset maestro para evitar colapsar los nodos del clúster durante consultas repetitivas. |
| **Calidad de Datos (Clima)** | Detección de nulos en las columnas climáticas de precipitación y condiciones, sin estrategia de mitigación. | Reemplazo condicional automatizado utilizando funciones nativas (`F.when`). Si `precip > 0`, se imputa `"rain"` y `"Rain"` de forma obligatoria. | **Casteo de Datos de Entrada:** Conversión de tipos de datos String a numéricos decimales (`DoubleType()`) para habilitar la entrada matemática en modelos estadísticos. |
| **Nivel 1: Analítica Descriptiva** | Conteos básicos y resúmenes descriptivos limitados de promedios, máximos y mínimos mediante `.describe()`. | Migración del procesamiento gráfico pesado. En lugar de procesar los 45 millones de filas en Pandas, se aplicó un muestreo estadístico controlado del 1% (`.sample(0.01)`). | **Estadísticos de Rúbrica:** Adición del cálculo de la **Mediana** y de los **Percentiles de Negocio (25 y 75)** mediante la función optimizada `F.percentile_approx()`. |
| **Nivel 2: Analítica Diagnóstica** | Enunciados de las 3 Hipótesis descritos únicamente en bloques de texto plano, sin respaldo empírico. | Migración del análisis de texto a código real mediante agregaciones y agrupaciones masivas (`.groupBy()`) para contrastar las variables de transporte vs factores externos. | **Métrica de Velocidad:** Creación en tiempo de ejecución de la métrica de eficiencia operacional corporativa: **Velocidad Promedio en Millas por Hora (MPH)**. |
| **Nivel 3: Analítica Predictiva** | Inexistente en la entrega anterior (Fase exploratoria). | Nueva Implementation: Se diseñó el flujo predictivo desde cero al pasar a la fase de modelado estadístico avanzado en Big Data. | **Modelado Estadístico:** Construcción, entrenamiento y validación de un modelo de Regresión Lineal utilizando la librería `scikit-learn`, evaluado mediante R^2 y RMSE en test, incluyendo la extracción de coeficientes individuales. |
| **Nivel 4: Analítica Prescriptiva** | Inexistente en la entrega anterior (Fase exploratoria). | Nueva Implementation: Desarrollo de los algoritmos de simulación económica en PySpark basados en los hallazgos del diagnóstico y el peso de las variables del modelo. | **Simulaciones de Impacto:** Creación de dos escenarios financieros matemáticos automatizados ("¿Qué pasaría si...?") y fijación de recomendaciones estratégicas amarradas a KPIs con metas numéricas medibles. |

---

## 5. Sustentación de los 4 Niveles de Analítica

### 📊 Nivel 1: Analítica Descriptiva
El cálculo avanzado con la función nativa distribuida `F.percentile_approx()` demostró los siguientes umbrales tarifarios en la organización:
* **Percentil 25 (Q1):** $9.50
* **Mediana (Q2):** **$14.20**
* **Percentil 75 (Q3):** **$18.50**

Esto confirma una marcada asimetría positiva en la recaudación: existe una base masiva de viajes cortos micro-urbanos pero estables, combinada con picos extremos aislados de larga distancia. Esta estructura justifica la urgencia de abandonar la tarifa plana o lineal tradicional e implementar un motor de precios adaptativo para capturar el excedente del consumidor en los cuartiles superiores. Los resultados e interacciones visuales de esta sección se encuentran completamente integrados dentro del componente interactivo `DashboardNeoCab.html`.

---

## 🔍 Nivel 2: Analítica Diagnóstica (Evolución y Validation de Hipótesis)

Para esta entrega final, el equipo migró de preguntas exploratorias iniciales (Fase 1) a hipótesis analíticas refinadas y cuantitativas (Fase 2), evaluándolas mediante agregaciones masivas sobre el clúster unificado:

#### 📌 Bloque de Investigación 1: Sensibilidad por Distrito y Factores Festivos
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿Es la demanda en distritos como Queens y Brooklyn más sensible a las bajas temperaturas que en Manhattan debido a las limitaciones de la red de transporte público alternativo?
* **Hipótesis Refinada Cuantitativa (Fase 2):** La demanda de viajes y el valor de la tarifa dinámica de NeoCab experimentan un incremento significativo durante los días feriados (*Holidays*) en los distritos clasificados como "hotspots" gastronómicos/nocturnos, debido a que la población local y los turistas concentran su movilidad exclusivamente en torno a actividades y consumo en estas zonas durante los días no laborables.
* **Resultado del Diagnóstico:** **VALIDADA.** Al agrupar por distrito y rangos térmicos, se demostró que cuando la temperatura desciende de los 10°C, la demanda en Queens y Brooklyn se dispara en un **14.2%** en comparación con días templados, mientras que Manhattan se mantiene estable por su densa red de metro. Además, al cruzar el flag de feriados, los cuadrantes clasificados como hotspots registraron un estiramiento en los cuartiles de precios, validando la concentración de la movilidad en las zonas de ocio nocturno.

<img width="580" height="326" alt="Captura de pantalla 2026-06-23 a la(s) 10 44 37 a  m" src="https://github.com/user-attachments/assets/701137ac-f5a1-49b6-a5f7-898b3efa13f0" />


#### 📌 Bloque de Investigación 2: Operación Eficiente vs. Reactiva bajo Lluvia
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿Provocan los días de lluvia intensa picos de tarifas dinámicas que NeoCab está perdiendo actualmente por no posicionar autos de forma preventiva en "hotspots"?
* **Hipótesis Refinada Cuantitativa (Fase 2):** NeoCab sufre una pérdida constante de ingresos potenciales al operar de forma reactiva y no posicionar vehículos de manera preventiva en las zonas comerciales de alta densidad gastronómica durante las horas de lluvia intensa, donde la coincidencia del mal clima y la salida de los locales de ocio genera picos de tarifa dinámica que la empresa pierde por tener a los conductores dispersos.
* **Resultado del Diagnóstico:** **VALIDADA.** Al cruzar las zonas de alta densidad con los registros de precipitación, el análisis de datos demostró que la tarifa cobrada promedio no compensa orgánicamente la escasez extrema de unidades en horas de tormenta. Esto comprueba que operar de manera reactiva dispersa la flota y genera una pérdida de ingresos potenciales debido a la falta de una política de posicionamiento de conductores previa a la salida de los locales de ocio.

<img width="530" height="311" alt="Captura de pantalla 2026-06-23 a la(s) 10 45 12 a  m" src="https://github.com/user-attachments/assets/c47f121c-bdd4-44a8-8b4d-1014752717da" />


#### 📌 Bloque de Investigación 3: Eficiencia Vial y Escasez en Eventos Complejos
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿La velocidad promedio de los viajes se reduce de forma crítica durante eventos de precipitación intensa (lluvia/nieve), afectando la rentabilidad por hora del conductor y la satisfacción del cliente?
* **Hipótesis Refinada Cuantitativa (Fase 2):** Los tiempos de espera de los usuarios y la escasez crítica de vehículos de NeoCab se concentran de manera predecible en los distritos de alta densidad gastronómica durante las horas de lluvia intensa en días feriados, debido a que la empresa opera de forma reactiva, ignorando que el empeoramiento del clima en periodos festivos alarga la duración de los viajes en curso y reduce la disponibilidad de la flota en las zonas de mayor demanda.
* **Resultado del Diagnóstico:** **VALIDADA.** Mediante el cálculo en tiempo de ejecución de la métrica de velocidad promedio, se comprobó que bajo lluvia intensa la velocidad de los autos cae drásticamente de **11.4 MPH a solo 6.1 MPH** (una reducción crítica del **46.5%**). Al unirse el factor de día feriado, esta congestión vial severa alarga los viajes activos, atrapando a las unidades en ruta e impidiendo que regresen a los hotspots de alta demanda, generando picos alarmantes en los tiempos de espera del usuario y un desabastecimiento generalizado en el sistema.
<img width="1177" height="306" alt="Captura de pantalla 2026-06-23 a la(s) 10 45 37 a  m" src="https://github.com/user-attachments/assets/56c4f6a2-bfac-45b1-b3ec-d39ed0d6caed" />




---

### 🤖 Nivel 3: Analítica Predictiva (Modelo de Machine Learning)

Se entrenó un modelo de **Regresión Lineal** utilizando la librería estadística `scikit-learn` alimentada por una muestra aleatoria controlada del dataset unificado de 45 millones de filas. Tras realizar la partición de datos (70% para entrenamiento y 30% para validación), el algoritmo arrojó las siguientes métricas de rendimiento y precisión reales:

* **Coeficiente de Determinación (R^2 en test):** **0.8277** | El modelo es capaz de explicar científicamente el **82.77%** de la variabilidad del precio de las tarifas de NeoCab basándose en las variables del entorno analizado.
* **Error Cuadrático Medio (RMSE en test):** **$6.58** | Establece el margen de desviación estándar promedio por predicción, indicando una alta precisión en la estimación de las tarifas operativas bajo el ecosistema de Spark.

#### Análisis de Coeficientes e Impacto de Variables
El código implementado extrajo exitosamente los coeficientes individuales de peso de cada variable de entrada (Celda 98). La ecuación matemática interna de predicción responde a los siguientes pesos netos directos escritos en texto plano:

**Tarifa Base Estimada = Intercepto + 2.81 * (es_hotspot) + 1.75 * (distancia) + 0.27 * (precip) - 1.09 * (es_feriado)**

El análisis determina que **la ubicación en hotspots de vida nocturna (es_hotspot) es la variable predictora con mayor peso operativo en el negocio, con un coeficiente de +$2.81**, seguida por la distancia del viaje (distancia) con un coeficiente de **+$1.75**. La condición de día feriado (es_feriado) muestra un coeficiente negativo de **-$1.09**, lo que indica que estructuralmente en días feriados la tarifa base tiende a contraerse si no se aplican correctores dinámicos algorítmicos (efecto condicionado por la multicolinealidad evaluada en la Celda 98). Todo el flujo de entrenamiento y desarrollo estadístico de este modelo está documentado paso a paso en el archivo de Databricks `TrabajoFINAL_NYC_grupo_1.ipynb`.

<img width="1217" height="763" alt="Captura de pantalla 2026-06-23 a la(s) 10 45 49 a  m" src="https://github.com/user-attachments/assets/408b476a-33cb-47f1-8675-aed00f52bc7e" />



---

### 📈 Nivel 4: Analítica Prescriptiva (Simulación de Escenarios)

Alineado con las métricas de impacto del modelo predictivo y los diagnósticos previos, se ejecutaron mediante código PySpark dos simulaciones analíticas del tipo *"¿Qué pasaría si...?"* para guiar las decisiones financieras de NeoCab:

* **Escenario Financiero 1 (Tarifa Dinámica Climatológica):** Filtrando los viajes históricos ocurridos en hotspots de vida nocturna bajo condiciones de lluvia intensa (154,050 viajes identificados) y aplicando un recargo dinámico simulado del **+15%**, el sistema calculó un **incremento proyectado de $642,172.31 dólares** para el periodo trimestral analizado (agosto-octubre 2021). 
    * *Nota:* Este cálculo corresponde al periodo de tres meses analizado. La proyección anualizada requeriría un análisis estacional adicional para ponderar los trimestres de invierno.
* **Escenario Operacional 2 (Redistribución de Flota por Shock Térmico):** Se simuló el desplazamiento preventivo forzado de un 10% de las unidades operativas de Manhattan hacia Brooklyn y Queens durante condiciones de frío (< 50°F). El volumen afectado en la periferia fue de 7,436,646 viajes con ingresos base de $165,890,322.89. Capturar un **8% de viajes adicionales** (594,931 viajes) generaría $13,272,910.61 brutos. Sin embargo, al contrastarlo contra el costo de oportunidad logístico y operativo de mantener inmovilizada la flota central ($16,589,032.29$), el modelo arrojó una **pérdida neta de -$3,316,121.68 dólares con un ROI negativo de -19.99%**, demostrando la inviabilidad financiera de una redistribución física rígida y forzada.

---

## 6. Recomendaciones Ejecutivas para NeoCab

### Trazabilidad: De los Datos a la Acción
Las siguientes recomendaciones están directamente conectadas con hallazgos cuantificados en este análisis. Cada recomendación sigue la cadena lógica formal: **Dato → Insight → Recomendación → Impacto Medible.**

## 🚀 Recomendación 1: Implementar Surge Pricing Inteligente en Hotspots de Vida Nocturna

### Hallazgo (Dato Cuantificado)
- **Métricas de Hipótesis 1:** En días feriados y zonas de vida nocturna, la demanda alcanza un promedio de **1,230,959 viajes diarios**, mientras que en condiciones basales desciende a **970,993 viajes** (**+26.8%**).
- La tarifa promedio actual en hotspots nocturnos se ubica en **\$24.77** frente a **\$24.19** en zonas basales, es decir, apenas un **+2.4%** de variación.
- **Métricas del Modelo Predictivo (Nivel 3):** El modelo de Machine Learning identifica que la variable binaria `es_hotspot` tiene un coeficiente positivo de **+\$2.81 USD por viaje**.

### Insight de Negocio
Los hotspots de vida nocturna durante fechas festivas experimentan una concentración masiva de demanda con alta disposición a pagar, junto con un incremento de congestión en ruta de **+16%**. Sin embargo, la tarifa orgánica de NeoCab solo sube **\$0.58**, lo que evidencia un desajuste algorítmico y una pérdida directa en la captura del excedente económico del consumidor en horas críticas.

### Acción Recomendada
Desplegar un **motor de tarifas dinámicas** parametrizado bajo tres reglas automatizadas:

1. **Regla Festiva:**  
   Multiplicador automático de **+15%** sobre la tarifa base en días feriados, en la ventana **18:00 - 02:00**, para zonas categorizadas como hotspots de ocio.

2. **Regla Climatológica:**  
   Incremento del **+10%** ante eventos de precipitación activa detectados por API meteorológica en tiempo real cuando la precipitación sea mayor a **2.5 mm**.

3. **Regla de Centralidad:**  
   Recargo fijo del **+8%** en horas pico direccionales de Manhattan:
   - **07:00 - 09:00**
   - **17:00 - 19:00**

### Mitigación
Ejecutar **pruebas A/B controladas** sobre el **20% de la base de usuarios activos** durante **60 días**, con el fin de calibrar la elasticidad precio-demanda.

### Impacto Financiero Proyectado
Tomando el volumen de **1,230,959 trayectos diarios** en feriados y un promedio de **10 feriados por trimestre**, se ven afectados **12,309,590 viajes**.  
Si la tarifa media sube de **\$24.77** a **\$28.49** (**+15%**), la recaudación bruta incremental sería de **\$45,791,675 trimestral**.  
Modelando una contracción prudente del **5% en la demanda** por efectos de elasticidad, se proyectan **ingresos netos ajustados anualizados de ~\$174 millones USD**.

- **Costo de TI:** \$500K  
- **ROI estimado:** **34,800%**

### KPIs Asociados
- **Tasa de aceptación de viajes con surge:** ≥ **88%**
- **Incremento en ingreso por viaje (RPT):** **+12%** vs. baseline
- **Elasticidad precio-demanda:** ≤ **5%** de caída de volumen por cada 10% de recargo
- **Satisfacción del cliente:** **NPS ≥ 65**
- **Deserción mensual:** ≤ **2%**

---

## 🚀 Recomendación 2: Modelo Híbrido de Incentivos Dinámicos para Cobertura Periférica

### Hallazgo (Análisis de Viabilidad Económica)
- **Métricas de Escenario 2 (Nivel 4):** El análisis de redistribución física forzada del **10% de la flota hacia Queens/Brooklyn** en días fríos (**< 50°F**) arrojó un volumen de **7,436,646 viajes** con ingresos actuales de **\$165,890,322.89**.
- Capturar un **8% adicional** mediante reubicación obligatoria generaría **594,931 viajes** e ingresos brutos por **\$13,272,910.61**.
- Sin embargo, el costo operacional logístico de sostener dicha flota inmovilizada asciende a **\$16,589,032.29**, generando una **pérdida neta de -\$3,316,121.68 USD** y un **ROI de -19.99%**.

### Insight de Negocio
La redistribución institucional forzada de la flota **no es financieramente viable**, porque el costo de oportunidad de retirar oferta de las zonas core de Manhattan es demasiado alto. La verdadera oportunidad no está en mover autos de forma centralizada, sino en construir un **modelo predictivo de incentivos algorítmicos opt-in**, que motive la distribución voluntaria y orgánica solo cuando el ratio de demanda local lo justifique.

### Acción Recomendada
Implementar **NeoCab FleetPredict**, un sistema de incentivos dinámicos basado en clima.

#### Fase 1: Detección Predictiva
Monitorear vía API climatológica los siguientes gatillos:
- **Temperatura < 50°F**
- **Precipitación > 2.5 mm/hr**
- **Ratio demanda/oferta en Queens/Brooklyn > 1.5x**

Cuando se cumplan estas condiciones, enviar **notificaciones push** a conductores libres en Manhattan con **2 a 4 horas de anticipación**.

#### Fase 2: Estructura de Estímulo
Asignar bonos variables de:
- **\$3.00 a \$5.00 por viaje** iniciado en la zona objetivo *(requiriendo completar al menos 2 viajes en el sector para calificar)*.
- **\$8.00 a \$12.00 por hora activa**.

#### Gamificación
- Insignias de **“Climate Hero”**
- Pozo de premios semanal de **\$500** para el **Top 10** de conductores con mayor tasa de respuesta geográfica.

### Impacto Financiero Proyectado
Al migrar de una política fija a una basada en incentivos dinámicos, donde el **25% de la flota responde de forma voluntaria**, se logra la misma captura del **+8%**, es decir, **594,931 viajes adicionales**. Esto generaría **\$13,272,910.61 en ingresos corporativos**.

El costo variable de bonos se reduce a **\$2,379,724**, calculado como:

`594,931 viajes × \$4.00 promedio`

Más un costo de infraestructura TI de **\$200,000**.

Esto transforma la pérdida previa en una **ganancia neta trimestral de \$10,693,186 USD**, proyectando aproximadamente **\$51 millones anuales**.

- **Costo anual total:** \$5.2M
- **ROI neto anual:** **255%**

### KPIs Asociados
- **Tasa de respuesta a alertas en la app:** ≥ **40%**
- **Tiempo promedio de reubicación geográfica:** ≤ **25 minutos**
- **Captura de demanda atendida en zona objetivo:** ≥ **85%** *(solicitudes completadas en menos de 5 min)*
- **Incremento en ingresos por conductor adherido:** **+15%**

---

## 🚀 Recomendación 3: Optimización de Turnos con Incentivos Dinámicos ("NeoCab Peak Rewards")

### Hallazgo (Dato Cuantificado)
- **Análisis temporal de demanda:** Los picos de movilidad se concentran en:
  - **07:00 - 09:00 (Pico AM)**
  - **17:00 - 20:00 (Pico PM)**
- El **Pico PM** concentra por sí solo el **35% de la demanda diaria total**.
- El volumen de viajes en estas ventanas es **2.5 veces mayor** que en horas valle.
- **Problema de oferta:** La disponibilidad de conductores **no escala proporcionalmente** con las horas pico, generando **tasas de rechazo elevadas** por falta de unidades concurrentes.

### Insight de Negocio
La desconexión de los conductores durante los picos de tráfico responde principalmente a:
1. **Fatiga vial**
2. **Falta de predictibilidad sobre sus ganancias horarias**

Al no existir un esquema de compensación por congestión, el conductor tiende a operar en horas valle. Por ello, estructurar un programa de incentivos escalonados permitiría **trasladar liquidez hacia las horas de mayor valor**, estabilizando el ecosistema de la plataforma.

### Acción Recomendada
Implementar el programa **NeoCab Peak Rewards**, compuesto por cuatro componentes tácticos:

#### Componente 1: Bonificaciones Horarias Diferenciadas
- **07:00 - 09:00 (Pico AM):**
  - **+\$2.00 por viaje**
  - **+\$8.00 por hora activa**

- **17:00 - 20:00 (Pico PM):**
  - **+\$3.00 por viaje**
  - **+\$12.00 por hora activa**

- **21:00 - 02:00 (Nocturno Finde):**
  - **+\$1.50 por viaje**
  - **+\$5.00 por hora activa**

#### Componente 2: Programa de "Streaks"
Incentivar la retención mediante metas de viajes consecutivos en horas pico:
- **5 viajes seguidos:** Bono de **\$25**
- **10 viajes seguidos:** Bono de **\$60**

#### Componente 3: Reserva de Turnos con Garantía
Permitir a conductores bloquear turnos de **3 horas** con **24 horas de anticipación**, garantizando un ingreso mínimo de:
- **\$80** en Pico AM
- **\$120** en Pico PM

#### Componente 4: Dashboard Predictivo del Conductor
Desplegar en la app un simulador financiero en tiempo real con proyección de ingresos, por ejemplo:

> “Si trabajas de 17:00 a 20:00, tu ganancia estimada es de \$145 - \$180”.

### Impacto Financiero Proyectado
Incrementar la disponibilidad de conductores activos en horas pico del **60% al 75%** añadiría **+750 unidades** al sistema, permitiendo procesar **4.86 millones de viajes adicionales al año**.

Esto se traduciría en un **ingreso bruto incremental anual de ~\$102 millones USD**.  
Descontando el costo del programa, estimado en **\$14.15M anuales** entre incentivos directos y fondos de garantía, el beneficio neto corporativo sería de **\$87.85 millones USD**.

- **ROI estimado:** **724%**

### KPIs Asociados
- **Porcentaje de flota activa y conectada en horas pico:** ≥ **75%**
- **Tiempo promedio de espera del usuario (ETA):** ≤ **4 minutos**
- **Tasa de cumplimiento de turnos reservados:** ≥ **85%**
- **Tasa de viajes completados vs. solicitados en horas pico:** ≥ **92%**
### Comparación Estratégica de Políticas Operacionales

| Aspecto | Redistribución Física (10%) | Modelo de Incentivos Dinámicos |
| :--- | :--- | :--- |
| **Costo Operativo** | Fijo: $16.6M / trimestre | Variable: Solo por viaje capturado ($2.37M) |
| **Flexibilidad** | Rígida (Conductor forzado) | Flexible (Decisión Opt-In voluntaria) |
| **Oferta Manhattan** | Reducida drásticamente (-10%) | Preservada (Solo migran unidades libres) |
| **ROI Comercial** | **-19.99%** (Destrucción de valor) | **+414%** (Rentabilidad de Big Data) |

---

## 7. Apéndice: Gobierno y Uso de Inteligencia Artificial Generativa

En cumplimiento de las políticas éticas y de transparencia del curso, el equipo declara el uso de herramientas de Inteligencia Artificial Generativa bajo el siguiente esquema de auditoría técnica:

* **Herramientas Utilizadas:** ChatGPT (OpenAI) y Gemini (Google).
* **Interacción y Prompts Clave:**
    * *Optimización de Consultas:* Conversión de filtros condicionales iterativos en estructuras masivas aceleradas mediante funciones nativas de PySpark (`F.when` y `F.percentile_approx`). Las implementaciones finales quedaron guardadas en el notebook distribuido `TrabajoFINAL_NYC_grupo_1.ipynb`.
    * *Traducción de Sintaxis:* Migración de código de entrenamiento estadístico estructurado en entornos locales (`scikit-learn`) hacia pipelines ejecutables a gran escala en los nodos del clúster distribuidos en Databricks.
    * *Estructuración de Reporte:* Transformación de bloques crudos de código e insights numéricos dispersos en un informe formal formateado limpiamente en Markdown para su correcta visualización en GitHub.
* **Verificación y Control de Calidad:** Todas las salidas algorítmicas, coeficientes de regresión obtenidos y proyecciones de escenarios financieros simulados fueron validados, auditados matemáticamente y corregidos de forma manual por el equipo de ingeniería (`QA`). Esto garantizó la coherencia lógica corporativa y previno cualquier tipo de alucinación informativa por parte de los modelos generativos.
