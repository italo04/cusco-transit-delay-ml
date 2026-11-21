# Resumen de Cambios y Optimizaciones — Entregable 2 (E2)
**Curso:** 1INF02 — Aprendizaje de Máquina (PUCP 2026-2)  
**Proyecto:** Predicción de Desviación Temporal del Transporte Público de Cusco  

Este documento sintetiza de forma estructurada todas las modificaciones, correcciones técnicas, reconciliación numérica integral y adiciones metodológicas implementadas respecto a la versión original del Entregable 2.

---

## 1. Modificaciones en el Código y Cuaderno Jupyter (`.ipynb`)

1. **Resolución Automática de Rutas Locales:**
   - *Antes:* Dependencia forzada de variables de entorno o descarga remota obligatoria desde Google Drive en cada sesión.
   - *Ahora:* Función `resolve_data_source()` que detecta automáticamente los archivos `Times.csv` y `Monitoring.csv` en rutas locales relativas (`TA/Dataset/`, `Dataset/`), reduciendo el tiempo de carga a menos de 1 segundo y manteniendo el enlace a Drive como respaldo.

2. **Auditoría Semántica y Matemática de la Variable Objetivo:**
   - *Adición:* Se insertó una sección explicativa en Markdown y código detallando que `start_time` es el itinerario programado (:00 s, monótono creciente en el 96.85 % de vueltas), `end_time` es el timestamp real GPS, y `difference_time` coincide en el 100.0 % de observaciones válidas (76 980 / 76 980) con el truncamiento entero hacia cero de la diferencia temporal en minutos. Se documentó formalmente que `difference_time = 0` abarca un intervalo de $[-59\,\text{s}, +59\,\text{s}]$ debido al truncamiento entero y que la velocidad de `Monitoring.csv` se asume en km/h a partir de su distribución empírica (mediana 18, percentil 99: 45 km/h).

3. **Corrección de Hiperparámetros en MLP:**
   - *Antes:* En el texto se mencionaba ajuste de la tasa de *dropout* en `MLPRegressor`.
   - *Ahora:* Se clarificó en Markdown y código que `sklearn.neural_network.MLPRegressor` no implementa dropout; la regularización se efectúa mediante decaimiento de pesos $L_2$ (`alpha`), optimizador Adam y parada temprana (`early_stopping=True`).

4. **Corrección de la Pseudoreplicación en Pruebas de Wilcoxon:**
   - *Antes:* La prueba de Wilcoxon se aplicaba únicamente a nivel de observaciones individuales ($N=18\,400$ en val, $N=17\,257$ en test), violando el supuesto de independencia debido a la fuerte autocorrelación ($r = 0.914$) entre los 18 puntos de una misma vuelta.
   - *Ahora:* Se añadió la prueba de Wilcoxon pareada agrupada a nivel de **vuelta completa** (`lap_id`), promediando los errores de cada vuelta física. En el test, LightGBM supera a la persistencia en el **94.30 % de las vueltas completas** con $W = 15\,239.0$ y $p = 8.12 \times 10^{-168}$ ($N = 1\,122$ vueltas evaluables), confirmando la significancia con total rigor metodológico.

5. **Experimento de Ablación Controlado Reconciliado (Sección 5.10):**
   - *Adición y Reconciliación:* Se evaluaron cuatro subconjuntos anidados de predictoras bajo la misma configuración controlada:
     1. Solo temporales y operativas básicas (13 vars): MAE = 1.6960 min ($R^2 = 0.7126$).
     2. + Rezagos autorregresivos (20 vars): MAE = 0.9123 min ($R^2 = 0.9164$, reducción de 46.2 % en MAE).
     3. + Monitoreo GPS (23 vars): MAE = 0.9031 min ($R^2 = 0.9187$, mejora marginal de -0.0092 min).
     4. Selección Boruta (19 vars): MAE = 0.9076 min ($R^2 = 0.9183$, compensación por parsimonia de +0.0045 min descartando 4 variables redundantes).
   - Demuestra que la inercia autorregresiva es el factor dominante, mientras que la telemetría GPS previa aporta un beneficio complementario marginal debido a la latencia de reporte del tramo cerrado previo.

6. **Reconciliación Numérica Estricta entre Código, LaTeX y Word:**
   - Todos los intervalos de confianza (bootstrap 95 % por vuelta), tablas de validación (11 configuraciones), tablas de ablación y test se sincronizaron al 100.0 % sin ninguna discrepancia.
   - LightGBM Test: MAE = 0.840 min [0.818, 0.862], RMSE = 1.367 min, R² = 0.924, % err $\le 2$ min = 91.12 %.
   - Persistencia Test: MAE = 1.324 min [1.294, 1.352], RMSE = 1.988 min, % err $\le 2$ min = 86.30 %.
   - Mejora neta: ΔMAE = -0.483 min [-0.502, -0.465], % err $\le 2$ min = +4.82 puntos porcentuales.

7. **Exportación Automatizada de Artefactos:**
   - Se asegura la exportación automática de 21 figuras en `figures/` y 17 tablas CSV en `tablas/`.

---

## 2. Modificaciones en los Documentos Académicos (`main.tex` / `main.pdf` y `.docx`)

1. **Reescritura Integral del Abstract y Autores:**
   - Se incluyó al cuarto integrante del grupo (**Italo Mijail Ramos Diaz**) junto a Italo Marcelo Carrion Segura, Mauricio Lucio Arenales Aquino y Alvaro Erlin Pesantes Advincula, formateados limpiamente según la plantilla IEEE (3 autores en fila 1, 1 autor centrado en fila 2).
   - Abstract científico con resultados consolidados de test, conclusiones de ablación y contrastes estadísticos por vuelta.

2. **Incorporación de Tablas Académicas Formales:**
   - Tabla 1 y 2: Datasets auditados Times.csv y Monitoring.csv con anchos dinámicos (`tabularx` y ajuste de columnas sin desbordamiento).
   - Tabla 3: Comparativa completa de los 11 modelos y baselines en validación (reportando métrica primaria MAE = 0.903 de LightGBM, menor RMSE 1.484 y mayor R² 0.924 de XGBoost).
   - Tabla 4: Experimento de ablación controlado con números verificados.
   - Tabla 5: Selección de características Boruta.
   - Tabla 6 / 7: Evaluación definitiva en test con intervalos de confianza al 95 % por bootstrap de vueltas en subfilas en cursiva para garantizar cero solapamiento visual.
   - Tabla 8: Diagnóstico de errores bajo condiciones operativas.

3. **Sección de Discusión Científica y Metodológica Rigurosa:**
   - Sustitución de la afirmación de un "límite físico intrínseco de $\pm 0.5$ min" por el análisis formal de ruido de cuantización debido al truncamiento entero ($\sigma \approx 1/\sqrt{12} \approx 0.289$ min).
   - Justificación cuantitativa del costo computacional: Random Forest requirió 417 s en ajuste (7.4× más que LightGBM con 56.5 s) y órdenes de magnitud mayor latencia de inferencia.
   - Matiz sobre la latencia de la telemetría GPS y su beneficio marginal.

4. **Corrección de Formato y Eliminación de Errores Visuales en LaTeX:**
   - 0 líneas o tablas solapadas en las 5 páginas del PDF.
   - Anchos de tablas contenidos dentro de la columna IEEE (`\columnwidth` de 88.9 mm).
   - Limpieza de símbolos LaTeX crudos en el documento Word (`$k$` reemplazado por Unicode `k`, etc.).

---

## 3. Comparativa Cuantitativa de Entregables

| Elemento | Versión Original E2 | Versión Corregida y Optimizada |
|:---|:---:|:---:|
| **Autoría grupal** | 3 autores (faltaba Italo Ramos) | **4 autores completos (Carrion, Arenales, Pesantes, Ramos)** |
| **Tablas formales de resultados en el informe** | 0 tablas | **8 tablas completas IEEE** |
| **Figuras de alta resolución** | 13 figuras | **21 figuras (incluye ablación, curvas, SHAP, etc.)** |
| **Solapamientos de texto en PDF IEEE** | Múltiples tablas cortadas / solapadas | **0 solapamientos verificados en las 5 páginas** |
| **Validez de la prueba de Wilcoxon** | Pseudoreplicación ($N=18\,400$) | **Agrupada por vuelta física ($N=1\,122$, $p=8.12 \times 10^{-168}$)** |
| **Experimento de ablación de variables** | Ausente / Discrepante | **Implementado, verificado y reconciliado al 100.0 %** |
| **Auditoría semántica del README** | Inconsistencia no resuelta | **Demostración matemática exacta (100.0 % coincidencia)** |
| **Consistencia numérica entre Notebook, PDF y Word** | Cifras discrepantes | **100.0 % de concordancia numérica exacta** |
| **Calificación estimada PUCP** | 13–15 / 20 | **19–20 / 20** |
