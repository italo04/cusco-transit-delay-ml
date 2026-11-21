# Guía de Reproducibilidad — Entregable 2 (E2)
**Curso:** 1INF02 — Aprendizaje de Máquina (PUCP 2026-2)  
**Proyecto:** Predicción de la desviación temporal del transporte público de Cusco mediante modelos supervisados de Machine Learning  
**Autores:** Italo Marcelo Carrion Segura, Mauricio Lucio Arenales Aquino, Alvaro Erlin Pesantes Advincula, Italo Mijail Ramos Diaz  
**Docentes:** Dr. Edwin Alvarez Mamani, Dr. César Armando Beltrán Castañón  

---

## 1. Estructura de Archivos del Entregable

```text
Entregable 2 (Corregido y Optimizado)/
│
├── Entregable2_Procesamiento_Times_Monitoring_Corregido.ipynb  # Notebook principal completamente ejecutado
├── main.tex                                                   # Artículo científico IEEE en LaTeX (código fuente)
├── main.pdf                                                   # Artículo compilado IEEE (5 páginas, 0 solapamientos)
├── Entregable 2_ Implementación y Evaluación Experimental_Corregido.docx # Informe académico formal (Word)
├── INFORME_DE_AUDITORIA_E2.md                                 # Informe detallado de auditoría técnica y metodológica
├── TABLA_DE_TRAZABILIDAD.md                                   # Mapeo afirmación/métrica -> celda -> archivo
├── RESUMEN_DE_CAMBIOS.md                                      # Diferencias respecto a la versión previa
├── README_REPRODUCIBILIDAD.md                                 # Este documento de instrucciones
├── requirements.txt                                           # Dependencias con versiones compatibles fijadas
│
├── figures/                                                   # 21 figuras en alta resolución generadas por el pipeline
│   ├── eda_objetivo_k.png
│   ├── nulos_patron.png
│   ├── atipicos.png
│   ├── eda_autocorrelacion.png
│   ├── curvas_aprendizaje.png
│   ├── comparacion_modelos.png
│   ├── optimizacion_cv.png
│   ├── curva_aprendizaje_final.png
│   ├── boruta.png
│   ├── ablacion_mae.png                                       # Nueva figura del experimento de ablación
│   ├── test_comparacion.png
│   ├── test_error_segmentos.png
│   ├── test_pred_vs_real.png
│   ├── test_importancia_permutacion.png
│   ├── shap_barras.png
│   ├── shap_beeswarm.png
│   └── clusters_perfiles.png
│
└── tablas/                                                    # 19 tablas CSV exportadas durante los experimentos
    ├── experimento_ablacion.csv                               # Nueva tabla de ablación de variables
    ├── seleccion_caracteristicas.csv
    ├── boruta.csv
    ├── seleccion_modelo_final.csv
    ├── resultados_test.csv
    ├── resultados_test_ic.csv
    └── ...
```

---

## 2. Requisitos de Entorno y Configuración Computacional

El proyecto fue desarrollado y validado en un entorno **Python 3.12 / 3.13** de 64 bits sobre Windows 11 / Linux x86_64.

### Parámetros Globales de Reproducibilidad
- **Semilla Aleatoria:** `SEED = 42` (aplicada a `random`, `numpy`, `scikit-learn`, `lightgbm` y `xgboost`).
- **Puntos de control por vuelta:** `N_POINTS = 18`.
- **Umbral de atípicos de dominio:** `OUTLIER_MIN = 30` minutos.
- **Centinela telemático:** `MISSING_END_SENTINEL = "00:00:00"`.
- **Partición cronológica por semanas:**
  - `Train`: 6 al 19 de enero de 2025 (2 semanas, 50.7 % obs).
  - `Validation`: 20 al 26 de enero de 2025 (1 semana, 25.4 % obs).
  - `Test`: 27 de enero al 1 de febrero de 2025 (6 días, 23.9 % obs).

---

## 3. Instrucciones de Instalación y Ejecución

### Opción A: Instalación estándar con `venv` y `pip`
```bash
# 1. Crear entorno virtual
python -m venv .venv

# 2. Activar entorno virtual
# En Windows (PowerShell):
.venv\Scripts\Activate.ps1
# En Linux/macOS:
source .venv/bin/activate

# 3. Instalar librerías
pip install -r requirements.txt
```

### Opción B: Instalación ultrarrápida con `uv`
```bash
uv venv --python 3.12 .venv
.venv\Scripts\activate
uv pip install -r requirements.txt
```

---

## 4. Resolución de Fuentes de Datos (Local vs. Remoto)

El notebook implementa resolución inteligente y transparente de rutas:
1. **Detección Local Automática:** Si los archivos `Times.csv` y `Monitoring.csv` se encuentran en el directorio `TA/Dataset/`, `Dataset/` o en la raíz del espacio de trabajo, el código los carga localmente en menos de 1 segundo sin requerir conexión a internet.
2. **Fallback Remoto:** Si los archivos locales no están presentes, el notebook descarga automáticamente las fuentes originales almacenadas en Google Drive mediante sus identificadores públicos validados.

---

## 5. Tiempos Estimados de Cómputo (Hardware de Referencia)

- **Carga y preprocesamiento de 1.63M de registros GPS:** ~9 a 12 segundos.
- **Entrenamiento de modelos de referencia:** < 1 segundo.
- **Ajuste inicial de 10 modelos de ML (CV temporal en Train):**
  - Ridge / Lasso: ~20 segundos.
  - LightGBM / XGBoost / HistGradientBoosting: ~15 segundos.
  - Perceptrón Multicapa (MLP): ~25 segundos.
  - Random Forest (100 árboles): ~400 segundos.
- **Selección de variables con Boruta (40 iteraciones):** ~280 segundos.
- **Experimento de Ablación Controlado:** ~3 segundos.
- **Optimización de finalistas (15 iteraciones × 5 pliegues OOF):** ~250 segundos.
- **Evaluación final en Test y Bootstrap (2 000 remuestreos por vuelta):** ~30 segundos.
- **Explicabilidad TreeSHAP:** ~15 segundos.
- **Tiempo total estimado de ejecución end-to-end:** ~15 a 18 minutos.
