# Data card: frases de noticias financieras en español

## Origen

- **Fuente:** [Kenpache/multilingual-financial-sentiment](https://huggingface.co/datasets/Kenpache/multilingual-financial-sentiment), Hugging Face.
- **Licencia:** Apache 2.0 (copia en `LICENSE_dataset.txt`). Permite usar, modificar y redistribuir los datos, siempre que se incluya la licencia y se reconozca la fuente.
- **Tipo de datos:** reales y públicos. Son frases de noticias ya publicadas por medios de comunicación.
- **Modificación:** el dataset original tiene 39.829 frases en varios idiomas. Este archivo (`frases_es.csv`) contiene solo las **7.125 frases en español** (`language == 'es'`), sin otros cambios.
- **Periodo:** la ficha original no lo indica. Algunas frases mencionan fechas de 2025 y 2026.
- **Etiquetado:** la ficha original **no explica cómo se etiquetaron** las frases (si fue de forma manual o con un modelo).

## Diccionario de datos

| Variable | Tipo | Descripción |
|---|---|---|
| `sentence` | texto | Frase de la noticia. Es el texto que leen los modelos. |
| `label` | categoría | Sentimiento de la frase: `negative`, `neutral` o `positive`, según si la noticia es desfavorable, neutral o favorable para el mercado, la empresa o la economía. Es la etiqueta a predecir. |
| `source` | categoría | Medio que publicó la noticia (14 medios). **No se usa en los modelos**, porque delata parcialmente la etiqueta. |
| `language` | categoría | Idioma de la frase. En este archivo siempre es `es`. |

## Población y cobertura

- **14 medios** de España y Latinoamérica, entre ellos Expansión, Cinco Días, El Confidencial, EFE Economía, Bloomberg Línea, El Financiero, DF.cl y Portafolio. El 24,7 % de las frases viene de Estrategias de Inversión.
- **Clases:** 53,9 % neutras, 23 % negativas y 23 % positivas. La clase crítica es la negativa.
- **Longitud:** mediana de 31 palabras, P95 de 59 y máximo de 80.

## Calidad (resultados de la auditoría)

| Hallazgo | Evidencia | Acción |
|---|---|---|
| Nulos y duplicados | Ninguno; ninguna frase aparece con dos etiquetas | Ninguna |
| ñ mal codificada (ń) | 6,2 % de las frases | Corregir ń → ñ al normalizar |
| Palabras pegadas ("sucesiónLa") | 11,9 % de las frases | Separarlas al normalizar |
| El medio delata la etiqueta | Expansión Empresas: 84 % neutras | Excluir `source` del modelo |
| Clases desbalanceadas | 53,9 % neutras | F1 macro y pesos por clase |
| Frases en inglés marcadas como español | 32 frases (0,45 %) | Documentar la limitación |
| Plantillas repetidas | Solo el 0,1 % comparte texto con otra etiqueta | Partición aleatoria estratificada |

## Datos personales (PII)

- **Sin correos electrónicos ni documentos de identidad.**
- **Teléfonos corporativos** en frases con formato de directorio de empresas, junto con nombres y cargos de ejecutivos.
- **Nombres de figuras públicas** (políticos, ejecutivos y analistas) citados en contexto periodístico, en cerca del 2,3 % de las frases (búsqueda aproximada).
- **Riesgo de privacidad:** bajo para el uso previsto, porque son noticias ya publicadas.

## Partición usada en el proyecto

Estratificada por etiqueta, con semilla 42: 60 % train (4.274 frases), 15 % validación (1.069) y 25 % test (1.782).

## Limitaciones

- No se conoce el método de etiquetado, y algunas frases tienen etiquetas discutibles: su sentimiento depende de la perspectiva.
- Hay frases fragmentadas o pegadas por la forma en que se extrajo el texto de las páginas web.
- El desempeño varía según el medio, y algunos medios tienen pocas frases.

## Usos previstos y no previstos

- **Previsto:** clasificar el sentimiento de noticias financieras públicas en español, con revisión humana de los casos dudosos.
- **No previstos:**
  - Perfilar o hacer seguimiento a personas o empresas específicas.
  - Procesar textos internos o de clientes sin anonimizarlos previamente.
  - Tomar decisiones de inversión de forma automática, sin revisión humana.
  - Clasificar textos que no sean noticias financieras, o en otros idiomas.
