# Informe Final de Analítica Avanzada y Big Data – NeoCab NYC (PC2)

**Curso:** AD3005 – Introducción a Data Analytics y Big Data (UTEC)  
**Caso 2:** Movilidad urbana y demanda de transporte  
**Grupo:** 1  

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
| **Infraestructura de Datos** | Carga aislada en Pandas/Spark de meses individuales (Agosto, Septiembre por separado). Celdas redundantes de verificación. | Unificación vertical de **45,931,107 de filas** mediante `.unionByName()`. Corrección de baches lógicos de fechas convirtiendo cadenas a tipo fecha real con `.to_date()`. | **Persistencia Avanzada:** Implementation de `.cache()` estratégico sobre el dataset maestro para evitar colapsar los nodos del clúster durante consultas repetitivas. |
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

Esto confirma una marcada asimetría positiva en la recaudación: existe una base masiva de viajes cortos micro-urbanos pero estables, combinada con picos extremos aislados de larga distancia. Esta estructura justifica la urgencia de abandonar la tarifa plana o lineal tradicional e implementar un motor de precios adaptativo para capturar el excedente del consumidor en los cuartiles superiores.

---

## 🔍 Nivel 2: Analítica Diagnóstica (Evolución y Validación de Hipótesis)

Para esta entrega final, el equipo migró de preguntas exploratorias iniciales (Fase 1) a hipótesis analíticas refinadas y cuantitativas (Fase 2), evaluándolas mediante agregaciones masivas sobre el clúster unificado:

#### 📌 Bloque de Investigación 1: Sensibilidad por Distrito y Factores Festivos
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿Es la demanda en distritos como Queens y Brooklyn más sensible a las bajas temperaturas que en Manhattan debido a las limitaciones de la red de transporte público alternativo?
* **Hipótesis Refinada Cuantitativa (Fase 2):** La demanda de viajes y el valor de la tarifa dinámica de NeoCab experimentan un incremento significativo durante los días feriados (*Holidays*) en los distritos clasificados como "hotspots" gastronómicos/nocturnos, debido a que la población local y los turistas concentran su movilidad exclusivamente en torno a actividades y consumo en estas zonas durante los días no laborables.
* **Resultado del Diagnóstico:** **VALIDADA.** Al agrupar por distrito y rangos térmicos, se demostró que cuando la temperatura desciende de los 10°C, la demanda en Queens y Brooklyn se dispara en un **14.2%** en comparación con días templados, mientras que Manhattan se mantiene estable por su densa red de metro. Además, al cruzar el flag de feriados, los cuadrantes clasificados como hotspots registraron un estiramiento en los cuartiles de precios, validando la concentración de la movilidad en las zonas de ocio nocturno.
<img width="951" height="531" alt="Captura de pantalla 2026-06-22 a la(s) 6 56 04 p  m" src="https://github.com/user-attachments/assets/fb8e5794-e7ca-4e06-8257-539a4bc48e55" />

#### 📌 Bloque de Investigación 2: Operación Eficiente vs. Reactiva bajo Lluvia
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿Provocan los días de lluvia intensa picos de tarifas dinámicas que NeoCab está perdiendo actualmente por no posicionar autos de forma preventiva en "hotspots"?
* **Hipótesis Refinada Cuantitativa (Fase 2):** NeoCab sufre una pérdida constante de ingresos potenciales al operar de forma reactiva y no posicionar vehículos de manera preventiva en las zonas comerciales de alta densidad gastronómica durante las horas de lluvia intensa, donde la coincidencia del mal clima y la salida de los locales de ocio genera picos de tarifa dinámica que la empresa pierde por tener a los conductores dispersos.
* **Resultado del Diagnóstico:** **VALIDADA.** Al cruzar las zonas de alta densidad con los registros de precipitación, el análisis de datos demostró que la tarifa cobrada promedio no compensa orgánicamente la escasez extrema de unidades en horas de tormenta. Esto comprueba que operar de manera reactiva dispersa la flota y genera una pérdida de ingresos potenciales debido a la falta de una política de posicionamiento de conductores previa a la salida de los locales de ocio.
<img width="935" height="521" alt="Captura de pantalla 2026-06-22 a la(s) 6 56 15 p  m" src="https://github.com/user-attachments/assets/aa352ba5-6b76-4968-a19f-183c5e94d0f5" />


#### 📌 Bloque de Investigación 3: Eficiencia Vial y Escasez en Eventos Complejos
* **Hipótesis Exploratoria Inicial (Fase 1):** ¿La velocidad promedio de los viajes se reduce de forma crítica durante eventos de precipitación intensa (lluvia/nieve), afectando la rentabilidad por hora del conductor y la satisfacción del cliente?
* **Hipótesis Refinada Cuantitativa (Fase 2):** Los tiempos de espera de los usuarios y la escasez crítica de vehículos de NeoCab se concentran de manera predecible en los distritos de alta densidad gastronómica durante las horas de lluvia intensa en días feriados, debido a que la empresa opera de forma reactiva, ignorando que el empeoramiento del clima en periodos festivos alarga la duración de los viajes en curso y reduce la disponibilidad de la flota en las zonas de mayor demanda.
* **Resultado del Diagnóstico:** **VALIDADA.** Mediante el cálculo en tiempo de ejecución de la métrica de velocidad promedio, se comprobó que bajo lluvia intensa la velocidad de los autos cae drásticamente de **11.4 MPH a solo 6.1 MPH** (una reducción crítica del **46.5%**). Al unirse el factor de día feriado, esta congestión vial severa alarga los viajes activos, atrapando a las unidades en ruta e impidiendo que regresen a los hotspots de alta demanda, generando picos alarmantes en los tiempos de espera del usuario y un desabastecimiento generalizado en el sistema.
<img width="936" height="501" alt="Captura de pantalla 2026-06-22 a la(s) 6 56 27 p  m" src="https://github.com/user-attachments/assets/c3602560-3692-466b-8bb0-0dcb038f940a" />


---

### 🤖 Nivel 3: Analítica Predictiva (Modelo de Machine Learning)

Se entrenó un modelo de **Regresión Lineal** utilizando la librería estadística `scikit-learn` alimentada por una muestra aleatoria controlada del dataset unificado de 45 millones de filas. Tras realizar la partición de datos (70% para entrenamiento y 30% para validación), el algoritmo arrojó las siguientes métricas de rendimiento y precisión reales:

* **Coeficiente de Determinación (R^2 en test):** **0.8277** | El modelo es capaz de explicar científicamente el **82.77%** de la variabilidad del precio de las tarifas de NeoCab basándose en las variables del entorno analizado.
* **Error Cuadrático Medio (RMSE en test):** **$6.58** | Establece el margen de desviación estándar promedio por predicción, indicando una alta precisión en la estimación de las tarifas operativas bajo el ecosistema de Spark.

#### Análisis de Coeficientes e Impacto de Variables
El código implementado extrajo exitosamente los coeficientes individuales de peso de cada variable de entrada (Celda 98). La ecuación matemática interna de predicción responde a los siguientes pesos netos directos escritos en texto plano:

**Tarifa Base Estimada = Intercepto + 2.81 * (es_hotspot) + 1.75 * (distancia) + 0.27 * (precip) - 1.09 * (es_feriado)**

El análisis determina que **la ubicación en hotspots de vida nocturna (es_hotspot) es la variable predictora con mayor peso operativo en el negocio, con un coeficiente de +$2.81**, seguida por la distancia del viaje (distancia) con un coeficiente de **+$1.75**. La condición de día feriado (es_feriado) muestra un coeficiente negativo de **-$1.09**, lo que indica que estructuralmente en días feriados la tarifa base tiende a contraerse si no se aplican correctores dinámicos algorítmicos (efecto condicionado por la multicolinealidad evaluada en la Celda 98).
<img width="931" height="819" alt="Captura de pantalla 2026-06-22 a la(s) 6 56 42 p  m" src="https://github.com/user-attachments/assets/02783e80-bbd4-4be7-bce4-0bf424dae96e" />


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

#### 🚀 Recomendación 1: Implementar Surge Pricing Inteligente en Hotspots de Vida Nocturna
* **Hallazgo (Dato Cuantificado):** 
    * *Métricas de Hipótesis 1 (Celda 95):* En días feriados + zonas de vida nocturna, la demanda alcanza 1,230,959 viajes diarios promedio, mientras que en días y zonas normales disminuye a 970,993 viajes diarios (un incremento del +26.8%). La tarifa promedio actual en hotspots nocturnos se ubica en $24.77 (frente a $24.19 en zonas basales).
    * *Métricas de Modelo Predictivo (Nivel 3):* El modelo estadístico de ML identifica que la variable es_hotspot ostenta un coeficiente positivo dominante de +$2.81 dólares por viaje.
* **Insight de Negocio:** Los hotspots de vida nocturna durante fechas festivas experimentan una concentración masiva de demanda (+26.8%) con una alta disposición a pagar y un incremento de congestión en ruta del +16% (Hipótesis 3). No obstante, la tarifa orgánica de NeoCab sube apenas un 2.4% ($0.58), evidenciando un desajuste y una clara pérdida en la captura del excedente económico del consumidor en horas críticas.
* **Acción Recomendada:** Desplegar un sistema de tarifas dinámicas parametrizado bajo tres reglas en producción:
    * *Regla Festiva:* Multiplicador automático del +15% sobre la tarifa base en días feriados (ventana 18:00 - 02:00) sobre zonas categorizadas como hotspots.
    * *Regla Climatológica:* Incremento del +10% ante eventos de precipitación activa detectados por API meteorológica en tiempo real (> 2.5mm).
    * *Regla de Centralidad:* Recargo fijo del +8% en horas pico direccionales de Manhattan (07:00-09:00 y 17:00-19:00), cuadrante que retiene el 60% de la demanda base de la app.
    * *Implementación:* Correr pruebas A/B controladas en el 20% de la base de usuarios activos durante 60 días para calibrar la elasticidad precio-demanda.
* **Impacto Financiero Proyectado:** Tomando el volumen de 1,230,959 trayectos diarios en feriados y un promedio de 10 feriados por trimestre, se afectan 12,309,590 viajes. Elevar la tarifa media de $24.77 a $28.49 (+15%) arroja una recaudación bruta incremental de $45,791,675 trimestral. Modelando una contracción prudente del 5% en la demanda por efectos de elasticidad, se proyeccionan ingresos netos ajustados anualizados de **~174 millones de USD**.
* **KPIs Asociados:**
    * *Tasa de Aceptación de Viajes con Surge:* Meta >= 88% (Monitoreo en tiempo real).
    * *Incremento en Ingreso por Viaje (RPT):* Meta +12% frente al baseline operativo.
    * *Elasticidad Precio-Demanda:* Meta <= 5% de caída de volumen por cada 10% de recargo.
    * *Satisfacción del Cliente (NPS) / Churn Rate:* Meta NPS >= 65 y deserción mensual <= 2%.

---

#### 🚀 Recomendación 2: Modelo Híbrido de Incentivos Dinámicos para Cobertura Periférica
* **Hallazgo (Análisis de Viabilidad Económica):**
    * *Métricas de Escenario 2 (Nivel 4, Celda 34):* El análisis analítico de redistribución física forzada del 10% de la flota hacia Queens/Brooklyn en días fríos (< 50°F) arrojó un volumen de 7,436,646 viajes con ingresos actuales de $165,890,322.89. Capturar un 8% adicional mediante reubicación forzada generaría 594,931 viajes e ingresos brutos por $13,272,910.61. Sin embargo, el costo operacional logístico de sostener dicha flota inmovilizada asciende a $16,589,032.29, resultando en una **ganancia neta destructiva de -$3,316,121.68 USD (ROI de -19.99%)**.
* **Insight de Negocio:** La redistribución física e institucional forzada de la flota no es financieramente viable debido al alto costo de oportunidad de retirar oferta de las zonas core de Manhattan, una captura marginal que no compensa los traslados vacíos y una tarifa promedio periférica inferior ($22.31). La verdadera oportunidad estratégica no radica en mover los autos de forma centralizada, sino en implementar un **modelo predictivo de incentivos algorítmicos opt-in** que motive la distribución voluntaria y orgánica solo cuando el ratio de demanda local lo justifique.
* **Acción Recomendada:** Implementar "NeoCab FleetPredict": Sistema de Incentivos Dinámicos Basado en Clima:
    * *Detección Predictiva (Fase 1):* Monitorear vía API climatológica umbrales de Temperatura < 50°F, precipitación > 2.5mm/hr y un ratio demanda/oferta en Queens/Brooklyn > 1.5x. Gatillar notificaciones push a conductores libres en Manhattan con 2-4 horas de anticipación.
    * *Estructura Estímulo (Fase 2):* Asignar bonos variables de $3.00 a $5.00 por viaje iniciado en la zona objetivo (requiriendo completar al menos 2 viajes en el sector para calificar) y un bono de hora activa de $8.00 a $12.00. 
    * *Gamificación:* Insignias de "Climate Hero" y un pozo de premios semanal de $500 para el Top 10 de conductores con mayor tasa de respuesta geográfica.
* **Impacto Financiero Proyectado (Modelo Híbrido Revisado):** Al transicionar de una política fija a una basada en incentivos dinámicos donde el 25% de la flota responde de forma voluntaria, se logra la misma captura del +8% (594,931 viajes adicionales) generando $13,272,910.61 en ingresos corporativos. El costo variable de los bonos se contrae a $2,379,724 (594,931 viajes x $4.00 promedio) más un costo de infraestructura TI de $200,000, **transformando la pérdida previa en una ganancia neta trimestral de $10,693,186 USD (ROI positivo de +414%)**, con una proyección anual de **42.8 millones de USD**.

### Comparación Estratégica de Políticas Operacionales

| Aspecto | Redistribución Física (10%) | Modelo de Incentivos Dinámicos |
| :--- | :--- | :--- |
| **Costo Operativo** | Fijo: $16.6M / trimestre | Variable: Solo por viaje capturado ($2.37M) |
| **Flexibilidad** | Rígida (Conductor forzado) | Flexible (Decisión Opt-In voluntaria) |
| **Oferta Manhattan** | Reducida drásticamente (-10%) | Preservada (Solo migran unidades libres) |
| **ROI Comercial** | **-19.99%** (Destrucción de valor) | **+414%** (Rentabilidad de Big Data) |
