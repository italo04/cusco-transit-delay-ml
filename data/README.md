# 📊 Datasets del Proyecto

Este directorio contiene la información tabular del sistema de transporte urbano de Cusco (enero a febrero de 2025).

## 1. Archivos

| Archivo | Filas | Columnas | Tamaño | Estado en Git | Descripción |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `Times.csv` | 4,707 vueltas (84,726 obs) | 39 | ~1.9 MB | **Incluido** | Registros de vueltas y tiempos de paso por los 18 puntos de control. |
| `Monitoring.csv` | 1,630,431 transmisiones | 6 | ~115.7 MB | **Excluido (.gitignore)** | Telemetría GPS de alta frecuencia (lat, lon, speed, bearing). |

## 2. Descarga de `Monitoring.csv`

Debido al límite de 100 MB por archivo en repositorios estándar de GitHub, `Monitoring.csv` debe colocarse en esta carpeta tras su descarga:

- **Enlace de descarga directa (Google Drive)**: [Descargar Monitoring.csv](https://drive.usercontent.google.com/download?id=1RkWfxHCiT-A3A-GK9LFC8CXpNW_EN90T&export=download&confirm=t)
- **Ruta esperada**: `data/Monitoring.csv` o `TA/Dataset/Monitoring.csv`.

El pipeline en el notebook detecta automáticamente las rutas locales antes de recurrir a la descarga remota.
