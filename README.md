# Análisis de Clientes y Segmentación — ConnectaTel

## 🎯 Objetivo del proyecto
Realizar un análisis integral de los clientes de **ConnectaTel** (empresa de telecomunicaciones) abarcando limpieza de datos, exploración estadística, evaluación de valores extremos y segmentación de usuarios. El propósito final es identificar patrones reales de consumo entre los planes `Basico` y `Premium`, detectar oportunidades comerciales y traducir los hallazgos en recomendaciones accionables para el negocio.

---

## 📂 Datasets utilizados
El análisis se apoya en dos fuentes de datos relacionales vinculadas mediante el identificador único de cliente (`user_id`):

1. **Tabla de usuarios (`users`) — 4,000 registros**
   - Contiene el perfil demográfico y contractual de cada cliente: `user_id`, `first_name`, `last_name`, `age`, `city`, `reg_date`, `plan` (`Basico` / `Premium`) y `churn_date`.
2. **Tabla de consumo (`usage`)**
   - Registra los eventos individuales de actividad de los usuarios (llamadas y mensajes de texto): `user_id`, `type` (`call` / `text`), `duration` (duración de llamadas en minutos) y `length` (longitud de mensajes).

Tras la agregación de métricas por cliente, ambas tablas se consolidaron en un dataset maestro (`user_profile`) con las variables de consumo `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada`.

---

## 🧭 Etapas del análisis realizadas

1. **Exploración inicial y diagnóstico de calidad:** Revisión de tipos de datos, valores faltantes, rangos inválidos y consistencia temporal.
2. **Limpieza y tratamiento de datos:**
   - `age`: Reemplazo de valores centinela (`-999`) por la mediana (**48 años**; rango final: 18–79 años).
   - `city`: Estandarización del carácter `?` como valor faltante (`pd.NA`), consolidando **565 registros faltantes (14.13%)**.
   - `reg_date`: Conversión de **40 fechas futuras (1.00%)** posteriores a 2024 a `NaT`.
   - `duration` y `length`: Verificación de nulos estructurales **MAR (*Missing At Random*)** dependientes del tipo de evento (`call` vs. `text`), conservados como `NaN` para evitar sesgos en promedios.
   - **Usuario inactivo:** Identificación de **1 usuario (0.025%)** sin registros en `usage`, imputado con `0` antes de la segmentación.
3. **Ingeniería de características y consolidación:** Agrupación del consumo a nivel usuario y unión (*left merge*) en `user_profile`.
4. **Análisis Exploratorio de Datos (EDA) y detección de *outliers*:**
   - Comparación visual de distribuciones entre planes (`Basico` vs. `Premium`).
   - Evaluación de *outliers* con *boxplots* y método **IQR**: se detectaron valores extremos únicamente en el extremo superior en `cant_mensajes` (**46 usuarios / 1.15%**, máx. 17), `cant_llamadas` (**30 usuarios / 0.75%**, máx. 15) y `cant_minutos_llamada` (**109 usuarios / 2.73%**, máx. 155.69 min). Se decidió **mantenerlos** por corresponder a clientes reales de alto consumo (*heavy users*).
5. **Segmentación de clientes:**
   - **Por nivel de uso (`grupo_uso`):** Uso medio **73.58% (2,943)** | Bajo uso **19.48% (779)** | Alto uso **6.95% (278)**.
   - **Por edad (`grupo_edad`):** Adulto **50.45% (2,018)** | Adulto Mayor **30.55% (1,222)** | Joven **19.00% (760)**.
6. **Insights ejecutivos y recomendaciones:** Traducción de los hallazgos en acciones comerciales (campañas de *upselling* a `Premium` para *heavy users* en `Basico`, rediseño de propuesta de valor y estrategias de retención para `Bajo uso`).

---

## ▶️ Cómo ejecutar el notebook

### Opción 1: Google Colab (recomendada, sin instalación local)
1. Ingresa a [Google Colab](https://colab.research.google.com/).
2. Selecciona **Archivo → Abrir cuaderno → GitHub** y pega la URL de este repositorio (o sube el archivo `.ipynb` directamente desde tu equipo).
3. Carga los archivos de datos originales en la sesión de Colab (panel izquierdo **Archivos**) o ajusta las rutas de lectura en la celda de carga de datos.
4. Ejecuta el cuaderno completo desde el menú **Entorno de ejecución → Ejecutar todas**.

### Opción 2: Entorno local (Jupyter Notebook / JupyterLab / VS Code)
1. Asegúrate de tener instalado **Python 3.8+** y las librerías necesarias.
2. Abre una terminal en la carpeta del proyecto e inicia Jupyter:
   ```bash
   jupyter notebook
   ```
3. Abre el archivo `.ipynb` y ejecuta las celdas en orden secuencial (`Kernel → Restart & Run All`).

---

## 🔁 Guía breve de reproducción

Para reproducir los resultados exactamente igual en tu equipo local:

1. **Clona o descarga el repositorio:**
   ```bash
   git clone https://github.com/<tu-usuario>/<nombre-del-repo>.git
   cd <nombre-del-repo>
   ```
2. **Crea un entorno virtual (opcional pero recomendado) e instala las dependencias:**
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
3. **Verifica las rutas de los datasets:** Coloca los archivos de datos (`users` y `usage`) en la ruta esperada por el notebook (o actualiza el `path` en la celda de carga inicial).
4. **Ejecuta todas las celdas en orden:** Desde la importación de librerías hasta las visualizaciones de segmentación para regenerar todas las tablas, métricas y gráficos sin errores de estado.

---

## 🛠️ Tecnologías utilizadas
- **Python 3**
- **Manipulación y análisis de datos:** `pandas`, `numpy`
- **Visualización:** `matplotlib`, `seaborn`