# Análisis de Mañaneras

Análisis de transcripciones de mañaneras (formato `.docx`) mediante conteo directo de menciones y análisis semántico con embeddings, comparando las presidencias de AMLO y Claudia Sheinbaum.

## Contenido del repositorio

| Archivo | Qué hace |
|---|---|
| `Analisis_Conteo_porGrupo.ipynb` | Conteo **exacto** (coincidencia literal de texto) de menciones para un grupo a la vez — personajes específicos (nombres propios) y palabras clave (temas). Fácil de editar: solo cambias el nombre del grupo, los personajes, las palabras clave y los títulos de las gráficas. |
| `Analisis_Embeddings.ipynb` | Análisis **semántico** con embeddings: detecta fragmentos que hablan de un grupo aunque no usen las palabras exactas, comparando el significado del texto contra frases descriptivas de cada grupo. |
| `Embeddings_Experimentacion.ipynb` | Notebook de calibración — para revisar, grupo por grupo, los fragmentos con mayor similitud y los que quedan justo debajo del umbral, y así ajustar las frases descriptivas y el umbral de similitud antes de correr el análisis final. |
| `requirements.txt` | Todas las dependencias necesarias para correr los tres notebooks. |

Los tres notebooks corren de forma **local** (no dependen de Google Colab / Google Drive).

## 1. Crear el entorno virtual

```bash
# 1. Crear el entorno virtual
python3 -m venv venv          # Windows: python -m venv venv

# 2. Activarlo
source venv/bin/activate      # Windows: venv\Scripts\activate

# 3. Instalar dependencias
pip install -r requirements.txt

# 4. Registrar el kernel para que aparezca en Jupyter
python -m ipykernel install --user --name=mananeras --display-name "Python (Mañaneras)"
```

Para volver a activar el entorno en sesiones futuras, solo repite el paso 2.

> ⚠️ `sentence-transformers` (usado por `Analisis_Embeddings.ipynb` y `Embeddings_Experimentacion.ipynb`) descarga un modelo de ~470 MB desde Hugging Face la primera vez que se usa — necesitas conexión a internet en esa primera ejecución.

## 2. Preparar los datos

Crea una carpeta `data/mananeras/` en la raíz del proyecto (no viene incluida en el repo) y coloca ahí tus archivos `.docx`. Cada nombre de archivo debe incluir el mes (abreviado a 3 letras, en español) y el año, por ejemplo:

```
ene-2019_mananera.docx
dic-2024_mananera.docx
```

Meses reconocidos: `ENE FEB MAR ABR MAY JUN JUL AGO SEP OCT NOV DIC` (mayúsculas o minúsculas).

Si prefieres tener tus `.docx` en Google Drive en vez de copiarlos a este proyecto, instala **Google Drive para escritorio** y apunta `RUTA_DATOS` (en la celda de configuración de cada notebook) a la ruta donde Drive monta la carpeta en tu computadora.

## 3. Orden recomendado para correr los notebooks

1. **`Embeddings_Experimentacion.ipynb`** (opcional, recomendado antes de confiar en resultados semánticos) — corre la carga y el cálculo de embeddings una vez, y revisa grupo por grupo los fragmentos de mayor similitud y los que quedan cerca del umbral. Ajusta `GRUPOS_EMBEDDINGS` y `UMBRAL_SIMILITUD` hasta que los resultados se vean razonables.
2. **`Analisis_Conteo_porGrupo.ipynb`** — edita la sección de CONFIGURACIÓN (`NOMBRE_GRUPO`, `PERSONAJES_CONTEO`, `PALABRAS_CLAVE`, títulos de gráficas) y corre todo. Genera:
   - Una gráfica con una línea por personaje (menciones exactas, por fecha).
   - Una gráfica con la suma de palabras clave (evolución mensual, normalizada por 1,000 palabras).
3. **`Analisis_Embeddings.ipynb`** — con el umbral ya calibrado, corre el análisis semántico completo y genera sus 3 gráficas (comparación por presidente, línea de tiempo mensual, heatmap trimestral).

Cada notebook guarda sus gráficas como `.png` en la carpeta raíz del proyecto.

## 4. Notas

- El conteo directo es *case-insensitive* y usa límites de palabra (`\b`), por lo que "prensa" no hará match parcial dentro de otra palabra.
- En `Analisis_Conteo_porGrupo.ipynb`, las menciones de personajes son un **conteo crudo** (no una tasa) — documentos más largos van a aparentar más menciones solo por tener más texto. Las palabras clave sí se normalizan por cada 1,000 palabras.
- El umbral de similitud semántica está pensado para calibrarse con datos reales — no lo tomes como definitivo sin antes revisar fragmentos concretos en `Embeddings_Experimentacion.ipynb`.
