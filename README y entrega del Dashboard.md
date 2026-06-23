# 🏙️ NeoCab NYC Analytics — Fase 2

**Optimización algorítmica de tarifas dinámicas y distribución de flota ante shocks climáticos**
Curso: Introducción a Data Analytics y Big Data · UTEC 2026-1

> **Pregunta de negocio:** ¿Estar en zona de bares o el calendario determina la demanda? Los datos revelan que la geografía multiplica la demanda por 6x, mientras que el feriado solo suma un +8.6% adicional. El pricing actual no lo refleja.

---

## 📊 Dashboard interactivo
- **[Ver Dashboard Analítico (DashboardNeoCab.html)](./DashboardNeoCab.html)** 🔗 — Haga clic aquí para ir directamente al archivo del dashboard interactivo alojado en la raíz de este repositorio. Desde allí podrá visualizar su historial de versiones o descargarlo para ejecutarlo localmente en cualquier navegador moderno.
- **`DashboardNeoCab.html`** — Versión interactiva completa con reportes geoespaciales y temporales embebidos en la raíz del proyecto. Se ejecuta localmente en cualquier navegador moderno sin necesidad de servidores activos.
- **Capturas estáticas** — Archivos `.png` independientes integrados directamente en este informe para lectura rápida y trazabilidad en GitHub.

👉 *Para explorar la analítica visual dinámica, descargue y ejecute `DashboardNeoCab.html`.*

---

## 1. Contexto Ejecutivo e Indicadores Clave

A través del procesamiento distribuido en Databricks de la data histórica de viajes HVFHHS (Agosto - Octubre), se identificaron las métricas de impacto clave que guían el rediseño algorítmico de NeoCab:

*   **Precisión del Modelo ML:** R² = 0.8277 en test (82.77% de la varianza de tarifas explicada).
*   **Impacto Anual Proyectado:** **\$327M USD** mediante la suma de las 3 recomendaciones estratégicas desplegadas.
*   **Demanda Hotspot / Feriado:** **6.4M** de viajes/día en picos frente a los ~1M observados en zonas normales.
*   **Incremento de Tarifa Hotspot:** **+\$2.81 USD** por viaje justificado por factor hotspot según el modelo de Machine Learning.
*   **Cuello de Botella Operacional:** **+15%** en la duración del viaje dentro del hotspot (19 min vs 16.8 min normal).

![Contexto Ejecutivo](Captura%201.png)

---

## 2. Validación de Hipótesis: ¿Dónde está el dinero real?

### Hipótesis 1: ¿Qué pesa más en la demanda, el feriado o estar en zona de bares?
Estar en un **Hotspot de Bares** multiplica los viajes por 6x (alcanzando 5.9M en días normales y 6.4M en feriados). En contraste, el efecto neto de que sea feriado dentro de la misma zona solo aporta un **+8.6%** de volumen incremental. El factor geográfico pulveriza por completo al calendario.

### Hipótesis 2: ¿Qué sube más la tarifa de NeoCab, estar en un hotspot o que llueva?
Cruzar el límite hacia un hotspot eleva automáticamente la tarifa en un promedio de **+\$4.50 USD (+22.9%)**. No obstante, cuando empieza a llover dentro del hotspot, la tarifa promedio apenas sube **+\$0.58 USD (+2.4%)**. El sistema ignora que el usuario en zona de bares está dispuesto a pagar mucho más bajo condiciones climáticas adversas.

![Validación de Hipótesis de Demanda y Tarifa](Captura%202.png)

---

## 3. Cuellos de Botella Operacionales en Clima Adverso

### Hipótesis 3: ¿Por qué NeoCab se queda sin vehículos justo en los hotspots con lluvia?
Los viajes originados en hotspots de bares toman consistentemente **~19.2 minutos**, en comparación con los 16.8 minutos de zonas normales (un **+15.4% de tiempo retenido**). 

Sorprendentemente, la lluvia *no* alarga los trayectos (incluso caen un sutil -1.3%). El colapso del servicio no ocurre porque los autos avancen más lento debido al agua, sino porque **los vehículos quedan atrapados por el tráfico interno de las zonas de ocio** y tardan más tiempo en liberarse para el siguiente viaje.

![Análisis de Cuello de Botella Operacional](Captura%203.png)

---

## 4. Validación Científica del Modelo Predictivo de Tarifas

Desarrollado bajo el Framework de Regresión Lineal en Spark MLlib (Muestra de Evaluación N=600).

*   **Valores Reales vs. Predichos (Test):** R² = 0.8277 demostrando un ajuste lineal altamente alineado con la Línea Base Ideal.
*   **Distribución de Errores (Residuos):** RMSE = \$6.58 con un Error Medio balanceado en 0, exhibiendo una distribución normal perfecta de los residuos.
*   **Análisis de Homocedasticidad:** El gráfico de dispersión de residuos versus tarifa predicha valida la estabilidad de la varianza a lo largo de los diferentes niveles de precio.

![Evaluación del Modelo de Regresión](Captura%204.png)

---

## 5. Detección de Anomalías Estadísticas (Multicolinealidad)

El modelo identificó un efecto colineal crítico en la variable `es_feriado`:
*   **El Conflicto:** El coeficiente asignado a la variable `es_feriado` arrojó un valor negativo inesperado de **-\$1.0855 USD**. Desde una perspectiva intuitiva de negocio, se esperaría que un feriado incremente los precios netos o mantenga un impacto neutral.
*   **La Explicación Técnico-Científica:** Esto se debe a un fenómeno de **multicolinealidad**. Dado que la inmensa mayoría de los viajes registrados en días festivos ocurren precisamente dentro de los hotspots de entretenimiento nocturno analizados, la variable `es_hotspot` (+\$2.8113) termina absorbiendo y canibalizando todo el peso predictivo positivo. El remanente negativo es un ajuste matemático del estimador multifactorial, no un decremento real en la tarifa de mercado.

![Identificación de Anomalía Estadística](Captura%205.png)

---

## 6. Plan de Acción: Tres Recomendaciones Estratégicas

Cada recomendación es directamente trazable a los datos y coeficientes del modelo validado para capturar un impacto total de **\$327M/año**:

1.  **Surge Pricing Inteligente en Hotspots de Vida Nocturna (\$174M/año):** El modelo valida +\$2.81/viaje atribuible al factor hotspot. Implementar un surge dinámico de +10% a +15% durante la interacción *lluvia x hotspot* captura la brecha actual de pricing no monetizada.
2.  **Incentivos Dinámicos por Turno — NeoCab Peak Rewards (\$102M/año):** Responde a los 6.4M viajes/día en picos de demanda elevando la disponibilidad de vehículos del 60% al 75% con incentivos escalonados por hora y zona, optimizando el balance oferta/demanda.
3.  **Reposicionamiento Predictivo de Flota por Alertas Climáticas (\$51M/año):** Los hotspots tienen viajes 15.4% más largos (19 vs 16.8 min) independientemente del clima. Reubicar autos proactivamente antes del colapso de oferta evita el déficit de disponibilidad en picos.

![Estrategia y Plan de Negocio](Captura%206.png)

---

## 🗂️ Estructura del repositorio
