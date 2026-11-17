# Normalización de Color en Imágenes H&E — Tejido de Pene

Repositorio de código correspondiente al trabajo de tesis de maestría:  
**"Evaluación comparativa de algoritmos de normalización de color en imágenes histológicas H&E de tejido de pene"**  
Universidad Comunera (UCOM) — Facultad de Ciencias de la Computación

---

## Descripción general

Este repositorio contiene el pipeline completo para:
1. Extraer parches de imágenes histológicas de tejido de pene en formato WSI.
2. Aplicar cuatro algoritmos de normalización de color H&E: Reinhard, Vahadane (SPCN), ACD (Zheng) y SCAN (Salvi).
3. Calcular métricas de evaluación: ΔE CIEDE2000, SSIM y tiempo de ejecución por parche.
4. Generar gráficos comparativos de los resultados.

---

## Estructura del repositorio

```
.
├── Pre_procesamiento_local_v2.ipynb      # Paso 1: extracción de parches
├── comparar_normalizacion_local_v2.ipynb # Paso 2: normalización y cálculo de métricas
├── graficos_metricas.ipynb               # Paso 3: generación de gráficos
├── acd_zheng.py                          # Módulo ACD (Zheng et al., 2019)
├── scan_salvi.py                         # Módulo SCAN (Salvi et al., 2020)
├── environment.yml                       # Entorno conda reproducible
└── README.md
```

> **Nota:** Las imágenes histológicas (parches fuente y referencia) y el archivo CSV de resultados no están incluidos en este repositorio por restricciones de tamaño y privacidad de los datos clínicos.

---

## Requisitos del sistema

| Componente | Versión recomendada |
|---|---|
| Sistema operativo | Windows 10/11, Ubuntu 20.04+ o macOS 12+ |
| Python | 3.10.12 |
| Conda | Miniconda o Anaconda (cualquier versión reciente) |
| RAM | 8 GB mínimo (16 GB recomendado) |
| Espacio en disco | 5 GB libres (entorno + datos) |

---

## Instalación del entorno

Se recomienda crear un entorno conda aislado para evitar conflictos con otras instalaciones de Python.

### 1. Instalar Miniconda (si no lo tenés)

Descargá el instalador desde: https://docs.conda.io/en/latest/miniconda.html  
Seguí las instrucciones del instalador para tu sistema operativo.

### 2. Clonar el repositorio

```bash
git clone https://github.com/<tu-usuario>/<nombre-del-repo>.git
cd <nombre-del-repo>
```

### 3. Crear el entorno conda

```bash
conda env create -f environment.yml
```

Este comando instala automáticamente Python 3.10.12 y todas las bibliotecas necesarias en un entorno llamado `hne-normalization`. El proceso puede tardar entre 5 y 15 minutos dependiendo de la conexión a internet.

### 4. Activar el entorno

```bash
conda activate hne-normalization
```

> **Importante:** este comando debe ejecutarse cada vez que se abra una nueva terminal antes de usar el proyecto.

### 5. Registrar el entorno como kernel de Jupyter

```bash
python -m ipykernel install --user --name hne-normalization --display-name "Python (hne-normalization)"
```

### 6. Iniciar Jupyter

```bash
jupyter notebook
```

Se abrirá el navegador con la interfaz de Jupyter. Desde ahí podés acceder a las notebooks.

---

## Preparación de los datos

Antes de ejecutar las notebooks, organizá tus archivos de la siguiente manera:

```
Fuentes_tesis/
├── laminas/           # Imágenes WSI originales (.tif o .svs)
├── parches/
│   ├── fuente/        # Parches extraídos de las láminas de estudio (.png)
│   └── referencia/    # Parche(s) de referencia target (.png)
├── csv/               # Carpeta donde se guardará el CSV de resultados
└── graficos/          # Carpeta donde se guardarán los gráficos generados
```

> Las rutas a estas carpetas se configuran al inicio de cada notebook en la sección **CONFIGURACIÓN**.

---

## Flujo de ejecución

Las notebooks deben ejecutarse en orden. Abrí cada una desde Jupyter y asegurate de seleccionar el kernel `Python (hne-normalization)`.

### Paso 1 — `Pre_procesamiento_local_v2.ipynb`

Extrae parches de las imágenes WSI utilizando `tiatoolbox`.

**Configurar al inicio del notebook:**
- `RUTA_LAMINAS`: ruta a la carpeta con las imágenes WSI.
- `RUTA_SALIDA`: ruta donde se guardarán los parches extraídos.
- Parámetros de extracción: tamaño de parche, nivel de resolución y umbral de tejido.

**Salida:** parches `.png` organizados por lámina en la carpeta de salida.

---

### Paso 2 — `comparar_normalizacion_local_v2.ipynb`

Aplica los cuatro algoritmos de normalización a cada parche y calcula las métricas.

**Configurar al inicio del notebook:**
- `RUTA_PARCHES`: ruta a la carpeta de parches extraídos en el paso 1.
- `RUTA_REFERENCIA`: ruta al parche de referencia (target).
- `RUTA_SALIDA_CSV`: ruta donde se guardará el archivo `resultados_consolidado_v2.csv`.

**Módulos utilizados (importados automáticamente desde el mismo directorio):**
- `acd_zheng.py` → clase `StainNormalizerACD` — algoritmo ACD (Zheng et al., 2019).
- `scan_salvi.py` → clase `StainNormalizerSCAN` — algoritmo SCAN (Salvi et al., 2020).
- `tiatoolbox` → normalización Reinhard.
- `wsi_normalizer` → normalización Vahadane/SPCN.

**Salida:** archivo CSV con columnas de ΔE CIEDE2000, SSIM y tiempo de ejecución por parche y por algoritmo.

---

### Paso 3 — `graficos_metricas.ipynb`

Genera los gráficos comparativos a partir del CSV de resultados.

**Configurar al inicio del notebook:**
- `RUTA_CSV`: ruta al archivo `resultados_consolidado_v2.csv` generado en el paso 2.
- `RUTA_SALIDA`: ruta donde se guardarán los gráficos `.png`.

**Salida:**
- `grafico_delta_e.png` — ΔE CIEDE2000 por algoritmo con escala perceptual.
- `grafico_ssim.png` — SSIM por algoritmo.
- `grafico_tiempo.png` — Tiempo de ejecución por parche.
- Identificación del parche con mejor, peor y rendimiento intermedio.

---

## Bibliotecas principales

| Biblioteca | Versión | Uso |
|---|---|---|
| tiatoolbox | 2.0.1 | Extracción de parches WSI y normalización Reinhard |
| wsi-normalizer | 1.0.1 | Normalización Vahadane/SPCN |
| tensorflow | 2.15.0 | Optimización matricial en ACD (Zheng) |
| scikit-learn | 1.4.0 | K-means en SCAN (Salvi) |
| scikit-image | 0.22.0 | Procesamiento de imágenes y cálculo de SSIM |
| numpy | 1.26.4 | Operaciones numéricas |
| pandas | 2.2.0 | Tabulación de resultados |
| pillow | 10.2.0 | Lectura y escritura de imágenes |
| matplotlib | 3.8.2 | Generación de gráficos |

---

## Algoritmos implementados

| Algoritmo | Módulo | Referencia |
|---|---|---|
| Reinhard | `tiatoolbox` | Reinhard et al. (2001). *Color transfer between images.* IEEE CGA. |
| Vahadane / SPCN | `wsi_normalizer` | Vahadane et al. (2016). *Structure-preserving color normalization.* IEEE TMI. |
| ACD | `acd_zheng.py` | Zheng et al. (2019). *Adaptive color deconvolution for histological WSI normalization.* CMPB. |
| SCAN | `scan_salvi.py` | Salvi et al. (2020). *Stain color adaptive normalization (SCAN) algorithm.* CMPB. |

---

## Solución de problemas frecuentes

**El entorno conda tarda mucho en crearse**  
Es normal en la primera instalación. TensorFlow y tiatoolbox son paquetes grandes. Si falla, intentá con:
```bash
conda env create -f environment.yml --no-deps
pip install -r requirements.txt
```

**Error: `No module named 'acd_zheng'` o `'scan_salvi'`**  
Verificá que `acd_zheng.py` y `scan_salvi.py` estén en el mismo directorio que las notebooks. Jupyter busca módulos relativos al directorio del notebook.

**Error de TensorFlow en Windows: `DLL load failed`**  
Instalá las Visual C++ Redistributables desde: https://aka.ms/vs/17/release/vc_redist.x64.exe

**El kernel de Jupyter no aparece como `Python (hne-normalization)`**  
Volvé a ejecutar el paso 5 de la instalación con el entorno activado.

---

## Licencia

Este código fue desarrollado con fines académicos. Para cualquier reutilización, citá el trabajo de tesis correspondiente.
