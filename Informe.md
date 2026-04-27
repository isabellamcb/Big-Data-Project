# Caso 2 - Movilidad urbana y demanda de transporte (NeoCab NYC)
<img src="fa1dc72c2fbc82e0a1734e721b1399f1.jpg" width="400"/>

**Índice**
Integrantes y Roles

Recursos del Proyecto

Descripción del Problema de Negocio

Descripción de los Datos

EDA Inicial: 9 Visualizaciones Clave

Hipótesis de Negocio (Etapa 2)

Plan de Trabajo Etapa 2 (Semanas 7–14)

Apéndice de Uso de IA


## Integrantes
-- **Bravo Chávez, Javier Alonso:** Market Strategy Lead (Redacción de hipótesis y definición del problema de negocio).

-- **Castillo Braschi, Isabella María**: Lead Data Engineer & Project Manager (Responsable de la organización del flujo de trabajo e interpretación de insights estratégicos).

-- **Corzo Quispe, Ernesto Rodrigo:** Business Intelligence Analyst (Diseño de KPIs y visualizaciones en Pandas).

-- **Godoy Castillo, Diego Alonso:** Data Quality Analyst (Validación de fuentes externas y apéndice de IA).

-- **Martell Lino, Giancarlos Arcadio:** Data Scientist (Desarrollo de modelos estadísticos y limpieza de datos).

*Nota de Colaboración:* Los roles descritos son referenciales para la gestión de tareas principales. El proyecto fue desarrollado de manera integral, permitiendo que los integrantes colaboren en áreas distintas a su rol asignado para fortalecer el resultado final del equipo.

## 🔗 Enlaces del Proyecto

Para facilitar el acceso a la infraestructura de datos y al entorno de procesamiento del equipo, se proporcionan los siguientes enlaces:

* **Google Colab Notebook:** [Trabajo NYC Grupo 1 - Análisis en PySpark](https://colab.research.google.com/drive/1Mh9Df7XjyCJlhm3doTC_IGvwZdGljDO-?usp=sharing)
* **Carpeta de Google Drive:** [Repositorio de Datasets y Recursos](https://drive.google.com/drive/folders/1WLhotdTzoXEg1bkHxg2nfkJO9Pz05xRg?usp=drive_link)
* **Presentación PPT:** [Presentación NeoCab - Grupo 1](https://canva.link/7m5ff3u9pcykow6)

---
# Empresa: NeoCab NYC 🗽🚕

## Descripción del problema de negocio
NeoCab NYC es una startup de movilidad urbana que busca posicionarse como una alternativa moderna, eficiente e inteligente dentro del competitivo mercado de transporte en la ciudad de Nueva York. 
La empresa cuenta con una flota moderna de vehículos, conductores afiliados y una plataforma digital para solicitar viajes, sin embargo, enfrenta una crisis operativa causada por la falta de uso estratégico de datos. 
**Actualmente, NeoCab toma decisiones de manera reactiva, sin anticiparse a cambios en la demanda generados por factores como el clima, la hora del día, la ubicación geográfica, el tráfico o eventos masivos en la ciudad.** 
Esta falta de visibilidad provoca problemas frecuentes como escasez de autos en zonas clave, largos tiempos de espera, pérdida de usuarios frente a competidores mejor posicionados y oportunidades desaprovechadas de ingresos en momentos de alta demanda. A ello se suma la dificultad para competir en precio y servicio frente a empresas consolidadas como Uber, Lyft o los taxis tradicionales de Nueva York.
En ese contexto, **el verdadero reto de NeoCab no es la falta de autos ni de mercado, sino la ausencia de inteligencia operativa que le permita anticiparse al comportamiento de la ciudad y responder con rapidez.** Para crecer de manera sostenible, la empresa necesita transformar grandes volúmenes de información en decisiones rentables y centradas en el cliente.
### El Valor de nuestra Base de Datos:
El principal activo estratégico de NeoCab no será únicamente su flota, sino su capacidad para convertir datos dispersos en conocimiento accionable. 
**Nuestra base de datos integra millones de registros históricos de viajes** con variables climáticas, temporales y geográficas, construyendo una visión completa del comportamiento de la demanda urbana en Nueva York. Gracias al procesamiento escalable con PySpark, esta información puede analizarse de forma eficiente y en tiempo real.


-- El valor de esta base de datos radica en que permite identificar patrones ocultos que antes pasaban desapercibidos. 
Por ejemplo, se puede detectar en qué distritos aumenta más la demanda cuando llueve, en qué horarios ciertos sectores presentan mayor rentabilidad, cuánto impacta el tráfico en la duración de los viajes o qué zonas requieren reposicionamiento preventivo de vehículos antes de un evento masivo. 
**Esto convierte a NeoCab en una empresa capaz de predecir, no solo reaccionar.**

Además, esta infraestructura de datos abre la puerta a múltiples ventajas competitivas: 
-- Implementar tarifas dinámicas más precisas
-- Reducir tiempos de espera
-- Mejorar la experiencia del usuario
-- Elevar ingresos por vehículo activo 
-- Optimizar decisiones estratégicas de expansión. 
En otras palabras, la base de datos es el puente que permitirá a NeoCab pasar de una startup tradicional de taxis a una compañía tecnológica de movilidad inteligente impulsada por analítica avanzada.

**Preguntas de Negocio:**
– 1. ¿Cómo varían la demanda y los precios de transporte según la hora, el clima y los eventos en la ciudad?
– 2. Cómo puede NeoCab NYC transformar su ceguera operativa en una ventaja competitiva mediante el análisis cruzado de demanda, geografía y clima?

## Descripciópn de datos
Para este análisis, se integraron tres fuentes de datos procesadas de manera escalable con **PySpark**, permitiendo manejar millones de registros:

### A. Fuente Principal: Registros de Viajes (df_uber_spark)
Dataset de la **NYC Taxi & Limousine Commission (TLC)** correspondiente a viajes de alto volumen (Uber/Lyft) de octubre 2021.
* **Variables clave:** `pickup_datetime`: Inicio del viaje.
    * `PULocationID` / `DOLocationID`: IDs de origen y destino.
    * `trip_miles` y `trip_time`: Distancia y duración del trayecto.
    * `base_passenger_fare`: Tarifa base, esencial para el análisis de precios.

### B. Enriquecimiento Climático (df_clima_spark)
Histórico meteorológico de **Visual Crossing** (2021-2024) que permite cruzar la demanda con el entorno ambiental.
* **Variables clave:** * `datetime`: Fecha del registro.
    * `tempmax`: Temperatura máxima en °C.
    * `precip`: Nivel de precipitaciones en mm.
    * `conditions`: Descripción del clima (ej. Rain, Clear).

### C. Referencia Geográfica (df_zonas_spark)
Diccionario de zonas de taxi (**Taxi Zone Lookup**) utilizado para la segmentación espacial de los datos.
* **Variables clave:**  `LocationID`: ID único para vinculación con los viajes.
    * `Borough`: Distrito administrativo (ej. Manhattan, Brooklyn, Queens).

## EDA Inicial
 EDA Inicial: Análisis de 9 Visualizaciones Clave
A través del análisis exploratorio en PySpark y visualizado en Pandas, identificamos patrones que validan nuestros indicadores de gestión (ver Colab para gráficos detallados):

1.  **Distribución de Millas:** Concentración masiva en viajes cortos (<5 millas). 
    * *KPI:* **Millas por Viaje**. El servicio es un complemento al transporte público.
2.  **Duración del Viaje:** La mayoría dura entre 10 y 25 minutos. 
    * *KPI:* **Eficiencia de Tiempo**. El tráfico limita la velocidad promedio.
3.  **Tarifa Base:** Agrupación predominante entre $10 y $25. 
    * *KPI:* **Ticket Promedio**. Modelo basado en alto volumen de bajo costo.
4.  **Demanda por Hora:** Pico sostenido de 2:00 PM a 8:00 PM. 
    * *KPI:* **Volumen Temporal**. Supera los niveles matutinos de oficina.
5.  **Demanda por Día:** Disparo de viajes los viernes y sábados. 
    * *KPI:* **Volumen por Jornada**. Preferencia para actividades sociales nocturnas.
6.  **Demanda vs. Temperatura Máxima:** Tendencia positiva hacia climas moderados. 
    * *KPI:* **Correlación Térmica**. Identifica umbrales de confort del usuario.
7.  **Demanda vs. Precipitación:** Picos de demanda inelástica en días lluviosos. 
    * *KPI:* **Sensibilidad Climática**. Oportunidad para tarifas dinámicas.
8.  **Demanda por Condición Climática:** Días despejados tienen demanda estable; condiciones mixtas provocan inestabilidad. 
    * *KPI:* **Previsibilidad Operativa**.
9.  **Demanda por Distrito (Borough):** Manhattan duplica a Brooklyn. 
    * *KPI:* **Cobertura Geográfica**. Foco crítico de saturación vs. oportunidades en periferia.

---

## Hipótesis de Negocio

Para la Etapa 2 de este proyecto, el equipo de **NeoCab NYC** se enfocará en validar las siguientes hipótesis mediante modelos predictivos:

**1.**¿Es la demanda en distritos como Queens y Brooklyn más sensible a las bajas temperaturas que en Manhattan debido a las limitaciones de la red de transporte público alternativo?
**2.**  ¿Provocan los días de lluvia intensa picos de tarifas dinámicas que NeoCab está perdiendo actualmente por no posicionar autos de forma preventiva en "hotspots"?
**3.** ¿La velocidad promedio de los viajes se reduce de forma crítica durante eventos de precipitación intensa (lluvia/nieve), afectando la rentabilidad por hora del conductor y la satisfacción del cliente?

---

## Plan de Trabajo Etapa 2 (Semanas 7–14)
El enfoque de esta etapa es transformar nuestra base de datos en una herramienta de **inteligencia en tiempo real**. No solo queremos saber qué pasó, sino preparar los datos para que NeoCab pueda anticiparse al futuro cercano.

| Semanas | Actividad Crítica: Potenciación y Enriquecimiento | Lo que lograremos | Responsable |
| :--- | :--- | :--- | :--- |
| **7 - 8** | **Creación de "Memoria" para la Data:** Desarrollaremos indicadores que comparen la demanda actual con la de las horas previas por zona (ej. ¿Subió la demanda en Brooklyn en la última hora?). | Dataset con patrones de comportamiento reciente. | I. Castillo |
| **9 - 10** | **Pruebas de Estrategia:** Validaremos estadísticamente si los picos de tarifa realmente se deben al clima o si hay otros factores ocultos que NeoCab está ignorando. | Reporte de certezas vs. suposiciones. | J. Bravo / G. Martell |
| **11 - 12** | **Conexión con el Entorno (Nuevos Datos):** Sumaremos información externa para que el sistema "entienda" el contexto de la ciudad (Tráfico, Eventos masivos, Años (2022, 2023, 2024) etc.). etc.). | Base de datos con visión 360°. | D. Godoy / E. Corzo |
| **13 - 14** | **Simulador de Decisiones:** Crearemos un tablero final (Dashboard) donde NeoCab pueda ver predicciones y mover su flota antes de que el problema ocurra. | Herramienta de control "Ceguera Cero". | Equipo Completo |

### Propuesta de Enriquecimiento (Nuevas fuentes para potenciar el negocio)
Para que NeoCab deje de operar "a ciegas", evaluaremos integrar estos datos adicionales:

1.  **Agenda de Grandes Eventos:** Datos de conciertos o partidos (ej. eventos en el Madison Square Garden). Esto explica por qué la demanda sube aunque el clima esté perfecto.
2.  **Estado del Tráfico en Tiempo Real:** Datos para entender cuánto tiempo extra pasará un conductor atrapado en tráfico, permitiendo ajustar la tarifa para que el conductor no pierda dinero.
3.  **Mapa de "Puntos de Calor" de la Ciudad:** Clasificar zonas según sean centros comerciales, oficinas o zonas de bares. Esto ayuda a saber *qué tipo* de cliente estamos atendiendo en cada hora.
4.  **Precios de la Competencia:** Información sobre las tarifas de taxis amarillos y otras apps para asegurar que NeoCab siempre sea la opción más inteligente para el usuario.

---

## Apéndice de Uso de IA
En la elaboración de este informe, hemos utilizado la IA para generar fragmentos de código específicos, permitiéndonos mantener el foco en el análisis crítico de los resultados y en la formulación de hipótesis de negocio.

### A. Prompts Principales Utilizados

#### 1. Limpieza y Preprocesamiento de Datos
* "Genera código PySpark para verificar valores nulos en el DataFrame `df_clima_spark` y convertir la columna 'datetime' a tipo timestamp. Luego, muestra el esquema y las primeras 5 filas para verificar la transformación."
* "Escribe código PySpark para inspeccionar `df_zonas_spark`: busca valores nulos en la columna 'Zone', muestra el esquema, las primeras 5 filas y lista los valores únicos de 'Borough' y 'service_zone'."
* "Proporciona el código para convertir los DataFrames de Spark `df_clima_spark` y `df_zonas_spark` a DataFrames de Pandas (`df_clima` y `df_zonas`) y muestra sus primeras filas."
* "Con el DataFrame de Pandas `df_clima`, verifica valores nulos, asegura que la columna 'datetime' sea de tipo datetime y muestra la información general (`info()`) y estadísticas descriptivas (`describe()`)."
* "Dado el DataFrame de Spark `df_uber_spark`, genera un muestreo del 1% y conviértelo a un DataFrame de Pandas (`df_uber`). Luego, extrae la hora del día (`pickup_hour`) y el día de la semana (`pickup_dayofweek`) de la columna `pickup_datetime`."

#### 2. Extracción de Características Temporales con PySpark
* "Usando `df_uber_spark`, extrae la hora del día (`pickup_hour`) y el día de la semana (`pickup_dayofweek`) de la columna `pickup_datetime`. Asegúrate de que el día de la semana esté indexado de Lunes (0) a Domingo (6). Muestra las primeras filas con las nuevas columnas."

#### 3. Unión de Datasets (Joins)
* "Genera código para calcular la demanda diaria de viajes a partir del DataFrame de Pandas `df_uber`. La columna resultante debe ser `total_trips` y la fecha de `pickup_datetime` debe ser la clave de unión. Luego, prepara el DataFrame `df_clima` para la unión, asegurándote de que tenga una columna de fecha compatible (`datetime_date`)."
* "Combina los DataFrames de Pandas `daily_uber_demand` y `df_clima` utilizando un inner join basado en las columnas de fecha (`pickup_date` y `datetime_date`). Muestra las primeras 5 filas del DataFrame resultante (`merged_df`)."
* "Realiza un inner join entre el DataFrame de Spark `df_uber_spark` y `df_zonas_spark` utilizando `PULocationID` y `LocationID` como claves. Luego, agrupa el resultado por `Borough` y cuenta el número de viajes para obtener la demanda por distrito."

#### 4. Generación de Visualizaciones
* "Crea un histograma con seaborn para visualizar la distribución de `trip_miles` en `df_uber`, limitando el eje X al 99º percentil para una mejor claridad."
* "Genera un histograma para la distribución de `trip_time` (convertido a minutos) en `df_uber`, con límites en el eje X para el 99º percentil."
* "Crea un gráfico de barras con seaborn que muestre la 'Demanda de Viajes por Hora del Día' (`pickup_hour`) utilizando `df_uber`."
* "Genera un gráfico de dispersión con línea de regresión (`sns.regplot`) para analizar la correlación entre `total_trips` y `tempmax` en `merged_df`."
* "Crea un gráfico de dispersión para `total_trips` vs `precip` en `merged_df`, usando el color (`hue`) para las `conditions` y el tamaño (`size`) para `total_trips`."
* "Visualiza la demanda de viajes por `Borough` utilizando un gráfico de barras a partir del DataFrame de Pandas `demand_by_borough_pd`, resultado de la agregación en Spark."

---

### B. Validación y Verificación de Resultados

El equipo implementó un proceso de validación continuo para asegurar la fiabilidad de los resultados generados con asistencia de la IA:

1.  **Verificación de Esquemas y Tipos de Datos:** Tras cada operación de transformación (especialmente conversiones Spark-Pandas), se utilizó `printSchema()` y `info()` para confirmar que los tipos de datos fueran los esperados (ej. datetime para fechas).
2.  **Inspección de Valores Nulos:** Se ejecutaron comandos como `df.filter(col('columna').isNull()).count()` en PySpark para confirmar que las estrategias de manejo de nulos fueran efectivas.
3.  **Concordancia de IDs y Claves de Unión:** En las uniones geográficas, se verificó que los `LocationID` coincidieran lógicamente con los `Boroughs` esperados mediante inspecciones intermedias con `show()`.
4.  **Estadísticas Descriptivas:** Se compararon los resultados de `describe()` con el conocimiento del dominio para asegurar que no hubiera anomalías (ej. rangos de temperatura imposibles o duraciones de viaje negativas).
5.  **Ejecución Técnica:** Se confirmó la ejecución exitosa de todos los fragmentos en Google Colab, corrigiendo cualquier discrepancia de sintaxis detectada en los tracebacks.
6.  **Análisis Lógico de Visualizaciones:** Cada gráfico fue revisado críticamente para asegurar que los patrones observados fueran lógicamente consistentes con la realidad operativa de Nueva York.
## Conclusión Inicial: Dinámica de la Demanda de Transporte en NYC

El análisis de los viajes de taxi y HVFHS (Uber/Lyft) en NYC para octubre de 2021, junto con datos climáticos y geográficos, revela patrones claros en la demanda de transporte:

### 1. Demanda Concentrada y de Corta Distancia
La mayoría de los viajes son de distancias cortas (menos de 5 millas) y duraciones de 10–25 minutos, lo que sugiere un uso enfocado en tramos internos y complementarios al transporte público.

### 2. Patrones Temporales Definidos
La demanda presenta un “doble pico”, con un incremento fuerte y sostenido entre las 2:00 PM y las 8:00 PM, indicando predominancia de viajes vespertinos y nocturnos (ocio y retorno a casa).  
Durante la semana (lunes a jueves) la demanda es estable, mientras que los viernes y sábados aumenta significativamente, reflejando su uso para actividades sociales.

### 3. Influencia del Clima
Las condiciones climáticas tienen un impacto notable en la demanda:
- Temperaturas moderadas → estabilizan la demanda  
- Lluvia → genera picos impredecibles (activando tarifas dinámicas)  
- Días despejados → muestran una demanda más alta y consistente frente a días de clima mixto  

### 4. Distribución Geográfica Desigual
Manhattan concentra la mayor demanda, casi duplicando a Brooklyn. Esto indica que, pese a la amplia red de transporte público, los usuarios priorizan conveniencia y rapidez.  
En los distritos exteriores, la demanda parece ser más sensible a factores climáticos, lo que puede afectar la rentabilidad de los conductores.

---

### Conclusión General
## Conclusión Inicial: Dinámica de la Demanda de Transporte en NYC

El análisis de los viajes de taxi y HVFHS (Uber/Lyft) en NYC para octubre de 2021, junto con datos climáticos y geográficos, revela patrones claros en la demanda de transporte:

### 1. Demanda Concentrada y de Corta Distancia
La mayoría de los viajes son de distancias cortas (menos de 5 millas) y duraciones de 10–25 minutos, lo que sugiere un uso enfocado en tramos internos y complementarios al transporte público.

### 2. Patrones Temporales Definidos
La demanda presenta un “doble pico”, con un incremento fuerte y sostenido entre las 2:00 PM y las 8:00 PM, indicando predominancia de viajes vespertinos y nocturnos (ocio y retorno a casa).  
Durante la semana (lunes a jueves) la demanda es estable, mientras que los viernes y sábados aumenta significativamente, reflejando su uso para actividades sociales.

### 3. Influencia del Clima
Las condiciones climáticas tienen un impacto notable en la demanda:
- Temperaturas moderadas → estabilizan la demanda  
- Lluvia → genera picos impredecibles (activando tarifas dinámicas)  
- Días despejados → muestran una demanda más alta y consistente frente a días de clima mixto  

### 4. Distribución Geográfica Desigual
Manhattan concentra la mayor demanda, casi duplicando a Brooklyn. Esto indica que, pese a la amplia red de transporte público, los usuarios priorizan conveniencia y rapidez.  
En los distritos exteriores, la demanda parece ser más sensible a factores climáticos, lo que puede afectar la rentabilidad de los conductores.

---
## Conclusión Inicial: Dinámica de la Demanda de Transporte en NYC

El análisis de los viajes de taxi y HVFHS (Uber/Lyft) en NYC para octubre de 2021, junto con datos climáticos y geográficos, revela patrones claros en la demanda de transporte:

### 1. Demanda Concentrada y de Corta Distancia
La mayoría de los viajes son de distancias cortas (menos de 5 millas) y duraciones de 10–25 minutos, lo que sugiere un uso enfocado en tramos internos y complementarios al transporte público.

### 2. Patrones Temporales Definidos
La demanda presenta un “doble pico”, con un incremento fuerte y sostenido entre las 2:00 PM y las 8:00 PM, indicando predominancia de viajes vespertinos y nocturnos (ocio y retorno a casa).  
Durante la semana (lunes a jueves) la demanda es estable, mientras que los viernes y sábados aumenta significativamente, reflejando su uso para actividades sociales.

### 3. Influencia del Clima
Las condiciones climáticas tienen un impacto notable en la demanda:
- Temperaturas moderadas → estabilizan la demanda  
- Lluvia → genera picos impredecibles (activando tarifas dinámicas)  
- Días despejados → muestran una demanda más alta y consistente frente a días de clima mixto  

### 4. Distribución Geográfica Desigual
Manhattan concentra la mayor demanda, casi duplicando a Brooklyn. Esto indica que, pese a la amplia red de transporte público, los usuarios priorizan conveniencia y rapidez.  
En los distritos exteriores, la demanda parece ser más sensible a factores climáticos, lo que puede afectar la rentabilidad de los conductores.

---

### Conclusión General
La demanda de transporte en NYC está fuertemente influenciada por:
- La hora del día  
- El día de la semana  
- Las condiciones climáticas  
- La ubicación geográfica  

Estos insights son clave para optimizar la distribución de conductores y diseñar estrategias de precios dinámicos en plataformas de movilidad.
```
