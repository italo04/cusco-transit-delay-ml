# Tabla de Trazabilidad Experimental — Entregable 2 (E2)
**Curso:** 1INF02 — Aprendizaje de Máquina (PUCP 2026-2)  
**Proyecto:** Predicción de Desviación Temporal del Transporte Público de Cusco  

La siguiente matriz documenta la trazabilidad estricta entre cada afirmación, tabla, figura y métrica presentada en el artículo científico (`.tex` / `.pdf` y `.docx`), su celda generadora en el cuaderno de código (`.ipynb`) y el archivo físico de salida correspondiente en los directorios `tablas/` o `figures/`.

---

| ID | Sección del Paper | Afirmación / Métrica / Resultado Clave | Celda del Notebook Corregido | Archivo de Salida (Figura / Tabla) | Variable / Objeto en Código |
|:---|:---|:---|:---:|:---|:---|
| **TR-01** | Resumen / 4.1 | 4 707 vueltas en Times.csv, expandidas a 84 726 observaciones de puntos de control (18 por vuelta). | Celda 5, 9, 15 | Salida estándar de texto | `len(times_raw)`, `len(obs)` |
| **TR-02** | 4.3 | Auditoría semántica: `start_time` monótono creciente en 96.85 % de vueltas (4 559 de 4 707). `difference_time` coincide exactamente en 100.0 % (76 980 / 76 980 con señal) con truncamiento `int((end - start)/60)`. | Celda 4 (MD), 21 | Salida estándar de texto | `diff_error.abs() < 1.0`, `is_monotonic` |
| **TR-03** | 4.4 | 7 746 valores faltantes (9.14 %) en `difference_time` coincidentes con centinela `00:00:00`. | Celda 18 | `figures/nulos_patron.png` | `obs["diff_missing"]`, `obs["end_sentinel"]` |
| **TR-04** | 4.4 | Regla de dominio: 238 atípicos (|d| > 30 min, 0.31 %); regla IQR habría descartado el 13.8 %. | Celda 24, 26 | `figures/atipicos.png` | `obs["is_outlier"]`, `obs["target"]` |
| **TR-05** | 4.4 | Depuración de Monitoring: 1 623 642 de 1 630 431 registros válidos (99.58 %) en Cusco. Velocidad asumida en km/h (mediana 18, p99 45). | Celda 29 | Salida estándar de texto | `mon_clean`, `ok_geo` |
| **TR-06** | 4.5 | 72 354 observaciones del modelo ($k \ge 2$ con target no nulo). Cero fuga de información temporal. | Celda 37 | Salida estándar de texto | `model_df` |
| **TR-07** | 4.5 | Cobertura telemática GPS: 78.4 % con $\ge 1$ registro en la ventana $[real(k-2), real(k-1)]$. | Celda 34 | Salida estándar de texto | `MON_COVERAGE`, `out["mon_n"]` |
| **TR-08** | 5.1 / Fig 1 | Distribución de la desviación: media 0.861 min, desv 4.975 min; asimetría y avance acumulativo por punto $k$. | Celda 44, 46 | `figures/eda_objetivo_k.png` | `d_ok["target"].describe()` |
| **TR-09** | 5.2 / Fig 4 | Autocorrelación serial: $r = 0.914$ con $k-1$ y $r = 0.849$ con $k-2$. | Celda 46 | `figures/eda_autocorrelacion.png` | `np.corrcoef(t, y_lag1)` |
| **TR-10** | 6.2 | Partición cronológica semanal: Train 36 697 obs (50.7 %), Val 18 400 (25.4 %), Test 17 257 (23.9 %). | Celda 37, 40 | Salida estándar de texto | `train`, `val`, `test` |
| **TR-11** | 7.1 / Tabla 3 | Desempeño en validación: LightGBM (MAE = 0.903 min, R² = 0.919) frente a Persistencia ($last\_known$, MAE = 1.431 min; estricta, MAE = 1.415 min) e Histórico (MAE = 2.760 min). XGBoost: MAE = 0.904, RMSE = 1.484, R² = 0.924. | Celda 54, 59, 64 | `figures/comparacion_modelos.png` | `RESULTS`, `comp` |
| **TR-12** | 7.1 / Fig 5 | Curvas de aprendizaje: convergencia de MLP (27 épocas) y pérdida L1 de LightGBM (train 0.809, val 0.903). RF requirió 417 s en tuning vs 56.5 s de LightGBM (7.4× más lento). | Celda 59, 62 | `figures/curvas_aprendizaje.png` | `boosting_curves()` |
| **TR-13** | 7.2 | Wilcoxon pareado por vuelta completa en validación ($N=1 192$ vueltas): $W = 21 405.0$, $p = 2.45 \times 10^{-152}$ (93.45 % de vueltas ganadas). | Celda 66 | `tablas/comparacion_wilcoxon_validacion.csv` | `p_lap`, `comp_stat` |
| **TR-14** | 7.3 / Tabla 5 | Selección Boruta: 17 confirmadas, 2 tentativas, 4 rechazadas (`lap`, `vehicle_id`, `dow_cos`, `mon_zero_share`). | Celda 73 | `figures/boruta.png`, `tablas/boruta.csv` | `bor`, `FEATS_BORUTA` |
| **TR-15** | 7.3 | Reevaluación Boruta en validación: MAE 19 vars = 0.9076 min (+0.0045 min vs 23 vars) con 17.4 % menor dimensionalidad. | Celda 75 | `tablas/seleccion_caracteristicas.csv` | `fs`, `delta` |
| **TR-16** | 7.4 / Tabla 4 | Experimento de ablación: Temporales (1.6960 min) $\to$ +Rezagos (0.9123 min, -46.2 %) $\to$ +GPS (0.9031 min, mejora marginal -0.009 min) $\to$ Boruta (0.9076 min). | Celda 77 | `figures/ablacion_mae.png`, `tablas/experimento_ablacion.csv` | `df_ablation`, `ablation_res` |
| **TR-17** | 7.5 / Tabla 6 | Optimización OOF en train+val: LightGBM MAE OOF = 0.9029 min (lr=0.0497, leaves=62, child=23, n_est=500). | Celda 78, 81 | `figures/optimizacion_cv.png`, `tablas/seleccion_modelo_final.csv` | `OPT`, `OOF`, `sel` |
| **TR-18** | 7.6 / Tabla 7 | Evaluación final en Test: LightGBM MAE = 0.840 min [0.818, 0.862], RMSE = 1.367 min [1.301, 1.439], R² = 0.924 [0.913, 0.933]. Persistencia: MAE = 1.324 min [1.294, 1.352], RMSE = 1.988 min [1.919, 2.068]. | Celda 89 | `figures/test_comparacion.png`, `tablas/resultados_test.csv` | `RES_TEST[FINAL_LABEL]`, `boot` |
| **TR-19** | 7.6 / Tabla 7 | Reducción neta vs Persistencia: ΔMAE = -0.483 min [-0.502, -0.465], % error $\le$ 2 min = 91.122 % vs 86.301 % (ganancia neta de +4.82 puntos porcentuales). | Celda 89 | `tablas/resultados_test_ic.csv` | `dmae`, `ic` |
| **TR-20** | 7.6 | Wilcoxon por vuelta completa en Test ($N=1 122$ vueltas): $W = 15 239.0$, $p = 8.12 \times 10^{-168}$ (94.30 % de vueltas ganadas). | Celda 89 | Salida estándar de texto | `p_w_lap`, `pct_laps_won` |
| **TR-21** | 7.7 / Tabla 8 | Diagnóstico de errores en test: MAE = 0.390 min si $|d|=0$; MAE = 1.545 min si $|d| \in [11, 30]$; lunes 93.6 % vs sábado 83.1 %. | Celda 91 | `figures/test_error_segmentos.png`, `tablas/test_error_magnitud.csv` | `t_mag`, `t_lag`, `t_ok` |
| **TR-22** | 7.8 / Fig 12 | Interpretabilidad SHAP y Permutación: `last_known`, `lag1` y `offset_min` dominan > 70 % de la importancia. | Celda 94 | `figures/shap_barras.png`, `figures/shap_beeswarm.png` | `shap_values`, `imp_te` |
| **TR-23** | 7.9 / Fig 13 | Clustering K-Means ($K=2$, silueta = 0.659): Grupo 1 adelanto madrugada (-9.10 min) vs Grupo 2 diurno regular (+1.61 min). | Celda 97 | `figures/clusters_perfiles.png`, `tablas/clusters_resumen.csv` | `t_cl`, `prof` |
