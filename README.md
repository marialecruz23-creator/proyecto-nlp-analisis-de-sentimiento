# Clasificación de sentimiento en noticias financieras en español

Proyecto final de Procesamiento de Lenguaje Natural · Universidad Ean · 2026

**Integrantes:** \Alexander · \Camilo · \María Alejandra

## Pregunta

¿Puede un modelo más complejo que el campeón de la Sesión 2 (TF-IDF de caracteres + regresión logística, `char\\\_35`) generar valor en la clasificación del sentimiento de noticias financieras?

Se compara el campeón con tres redes recurrentes (Simple RNN, GRU y LSTM) y dos modelos preentrenados (Beto y Roberta XLM-R), con la misma partición y las mismas métricas. La clase crítica es la **negativa**.

## Estructura del repositorio

```text
├── README.md               Este archivo
├── requirements.txt        Librerías y versiones
├── LICENSE                 Licencia del código (Apache 2.0)
├── .env.example            Variables de configuración (sin secretos)
├── notebooks/
│   ├── proyecto\\\_final.ipynb   Auditoría, entrenamiento, evaluación, errores y conclusiones
│   └── demo.ipynb             Demo: clasifica una frase con confianza y abstención
├── data/
│   ├── frases\\\_es.csv          Frases en español del dataset original
│   ├── data\\\_card.md           Ficha del dataset: origen, diccionario, limitaciones
│   └── LICENSE\\\_dataset.txt    Licencia del dataset original (Apache 2.0)
└── resultados/             Tablas de métricas, curvas y registro de experimentos
```

## Cómo ejecutarlo

1. Abrir `notebooks/proyecto\\\_final.ipynb` en Google Colab y activar la GPU (**Entorno de ejecución → Cambiar tipo de entorno → T4 GPU**).
2. Instalar las dependencias: `pip install -r requirements.txt`.
3. Revisar la celda de configuración al inicio del notebook (`RUTA\\\_BASE`). Por defecto, todo se guarda en carpetas relativas al proyecto.
4. Ejecutar el notebook de principio a fin. El dataset se descarga desde Hugging Face.
5. Para la demo, abrir `notebooks/demo.ipynb` y ajustar `RUTA\\\_MODELO` a la carpeta donde quedó guardado el modelo Roberta (XLM-R).

Los modelos entrenados no se incluyen en el repositorio por su tamaño (Beto \~440 MB, XLM-R \~1,1 GB). Se generan al ejecutar el notebook.

## Resultados principales (test, 1.782 frases)

|Modelo|F1 macro|Recall negativo|Latencia p95|
|-|-|-|-|
|Campeón `char\\\_35`|0,646|0,69|7,8 ms (CPU)|
|Simple RNN|0,509|0,63|373 ms (CPU)|
|GRU|0,504|0,42|696 ms (CPU)|
|LSTM|0,552|0,74|560 ms (CPU)|
|Beto|**0,761**|0,74|9,5 ms (GPU)|
|Roberta (XLM-R)|0,745|**0,81**|10,0 ms (GPU)|

Con umbral de abstención (elegido en validación y aplicado una sola vez en test):

|Modelo|Umbral|Abstención|Recall crítico|
|-|-|-|-|
|Campeón `char\\\_35`|0,45|17,6 %|0,76|
|Beto|0,80|23,3 %|0,83|
|Roberta (XLM-R)|0,65|14,8 %|0,85|

**Recomendación:** pilotear Roberta (XLM-R) en *shadow mode*, manteniendo el campeón `char\\\_35` como respaldo. Cumple tres de los cuatro criterios del prerregistro; no alcanza el recall crítico de 0,90 con el umbral en test. Ver la sección de conclusiones del notebook.

## Reproducibilidad

* **Semilla:** 42 en todos los modelos y en la partición.
* **Partición:** estratificada, 60 % train (4.274), 15 % validación (1.069) y 25 % test (1.782). Test se usó una sola vez, después de seleccionar con validación.
* **Hardware:** campeón y redes recurrentes en CPU; Beto y XLM-R en GPU Tesla T4 (Google Colab).
* **Tiempo de entrenamiento:** campeón 13 s · Simple RNN 22 s · GRU 46 s · LSTM 41 s · Beto 332 s · XLM-R 508 s.
* **Hiperparámetros, versiones y tiempos:** en `resultados/registro.csv` y `requirements.txt`.
* Las redes recurrentes varían entre corridas: con la misma configuración, el F1 macro de la LSTM en test pasó de 0,596 a 0,552.

## Datos y licencias

* **Dataset:** [Kenpache/multilingual-financial-sentiment](https://huggingface.co/datasets/Kenpache/multilingual-financial-sentiment) (Hugging Face), licencia Apache 2.0. Se usan solo las 7.125 frases en español; el archivo `data/frases\\\_es.csv` es una versión filtrada del original. Son datos reales y públicos (noticias ya publicadas). Detalles en `data/data\\\_card.md`.
* **Código:** licencia Apache 2.0 (ver `LICENSE`).

## Declaración de uso de IA generativa

* **Claude (Anthropic):** apoyo en la explicación de conceptos, depuración y escritura de código, revisión de las interpretaciones contra las salidas del notebook para verificar errores de escritura o de interpretación, y redacción de borradores de documentación.



Todo el código fue ejecutado por el equipo, y las cifras reportadas provienen de las salidas del notebook. Las decisiones de diseño, las interpretaciones y la recomendación son responsabilidad del equipo. No se enviaron datos confidenciales a servicios externos.

