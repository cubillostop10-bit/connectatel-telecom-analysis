# Análisis de Clientes y Segmentación — ConnectaTel

## 🎯 Objetivo del proyecto
Realizar un análisis integral de los clientes de **ConnectaTel** (empresa de telecomunicaciones en Latinoamérica) con información registrada hasta 2024, abarcando limpieza de datos, exploración estadística, evaluación de valores extremos y segmentación de usuarios. El propósito final es identificar patrones reales de consumo entre los planes `Basico` y `Premium`, detectar oportunidades comerciales y traducir los hallazgos en recomendaciones accionables para el negocio.

---

## 📂 Datasets utilizados
El análisis se apoya en tres fuentes de datos relacionales:

1. **Planes (`plans.csv`) — 2 registros**
   - Detalle comercial de los planes `Basico` y `Premium`: `plan_name`, `messages_included`, `gb_per_month`, `minutes_included`, `usd_monthly_pay`, `usd_per_gb`, `usd_per_message` y `usd_per_minute`.
2. **Usuarios (`users_latam.csv` / `users`) — 4,000 registros**
   - Perfil demográfico y contractual de cada cliente: `user_id`, `first_name`, `last_name`, `age`, `city`, `reg_date`, `plan` (`Basico` / `Premium`) y `churn_date`.
3. **Consumo (`usage.csv` / `usage`) — 40,000 registros**
   - Eventos individuales de actividad de los usuarios (llamadas y mensajes de texto): `id`, `user_id`, `type` (`call` / `text`), `date`, `duration` (duración de llamadas en minutos) y `length` (longitud de mensajes).

Tras la agregación de métricas por cliente (`user_id`), se construyó el dataset maestro `user_profile` con las variables consolidadas `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada`.

---

## 🧭 Etapas del análisis realizadas

1. **Exploración inicial y diagnóstico de calidad:** Revisión de estructura, tipos de datos, valores faltantes, valores centinela y consistencia temporal.
2. **Limpieza y tratamiento de datos:**
   - `age`: Reemplazo de **55 valores centinela `-999` (1.38%)** por la mediana (**48 años**; rango final: 18–79 años).
   - `city`: Estandarización del carácter `?` (**96 registros / 2.40%**) como valor faltante (`pd.NA`), consolidando **565 registros faltantes (14.13%)**.
   - `reg_date`: Conversión de **40 fechas futuras (1.00%)** posteriores a 2024 a `NaT`.
   - `duration` y `length`: Verificación de nulos estructurales **MAR (*Missing At Random*)** dependientes del tipo de evento (`call` vs. `text`), conservados como `NaN` para evitar sesgos en promedios.
   - **Usuario sin actividad:** Identificación de **1 usuario (0.025%)** sin registros en `usage`, imputado con `0` antes de la segmentación para evitar clasificarlo erróneamente como *Alto uso*.
3. **Ingeniería de características y consolidación:** Agrupación del consumo a nivel usuario y unión (*left merge*) en `user_profile`.
4. **Análisis Exploratorio de Datos (EDA) y detección de *outliers*:**
   - Comparación visual de distribuciones entre planes (`Basico` vs. `Premium`), confirmando ausencia de diferenciación real en el comportamiento de consumo.
   - Evaluación de *outliers* con *boxplots* y método **IQR**: se detectaron valores extremos únicamente en el extremo superior en `cant_mensajes` (**46 usuarios / 1.15%**, máx. 17), `cant_llamadas` (**30 usuarios / 0.75%**, máx. 15) y `cant_minutos_llamada` (**109 usuarios / 2.73%**, máx. 155.69 min). Se decidió **mantenerlos** por corresponder a clientes reales de alto consumo (*heavy users*).
5. **Segmentación de clientes:**
   - **Por nivel de uso (`grupo_uso`):** Uso medio **73.58% (2,943)** | Bajo uso **19.48% (779)** | Alto uso **6.95% (278)**.
   - **Por edad (`grupo_edad`):** Adulto **50.45% (2,018)** | Adulto Mayor **30.55% (1,222)** | Joven **19.00% (760)**.
6. **Insights ejecutivos y recomendaciones:** Traducción de hallazgos en acciones comerciales (campañas de *upselling* hacia `Premium` para *heavy users* en `Basico`, rediseño de propuesta de valor y estrategias de retención para `Bajo uso`).

---

## ▶️ Cómo ejecutar el notebook

### Opción 1: Google Colab (recomendada, sin instalación local)
1. Ingresa a [Google Colab](https://colab.research.google.com/).
2. Selecciona **Archivo → Abrir cuaderno → GitHub** y pega la URL de este repositorio (o sube `connectatel_analysis.ipynb` directamente desde tu equipo).
3. Carga los archivos CSV originales (`plans.csv`, `users_latam.csv` y `usage.csv`) en la sesión de Colab (panel izquierdo **Archivos**) o ajusta las rutas en la celda de carga de datos.
4. Ejecuta el cuaderno completo desde **Entorno de ejecución → Ejecutar todas**.

### Opción 2: Entorno local (Jupyter Notebook / JupyterLab / VS Code)
1. Asegúrate de tener instalado **Python 3.8+** y las librerías requeridas.
2. Abre una terminal en la carpeta del proyecto e inicia Jupyter:
   ```bash
   jupyter notebook
   ```
3. Abre `connectatel_analysis.ipynb` y ejecuta todas las celdas en orden (`Kernel → Restart & Run All`).

---

## 🔁 Guía breve de reproducción

Para reproducir los resultados exactamente igual en tu equipo local:

1. **Clona o descarga el repositorio** *(reemplaza los datos por tu usuario y repositorio)*:
   ```bash
   git clone https://github.com/<tu-usuario>/<nombre-del-repo>.git
   cd <nombre-del-repo>
   ```
2. **Instala las dependencias:**
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
3. **Verifica las rutas de los datasets:** Coloca `plans.csv`, `users_latam.csv` y `usage.csv` en la ruta esperada por el notebook (`/datasets/`) o actualiza el `path` en la celda de carga inicial.
4. **Ejecuta todas las celdas en orden secuencial** para regenerar las tablas, métricas y visualizaciones.

---

## 🛠️ Tecnologías utilizadas
- **Python 3**
- **Manipulación y análisis de datos:** `pandas`, `numpy`
- **Visualización:** `matplotlib`, `seaborn`