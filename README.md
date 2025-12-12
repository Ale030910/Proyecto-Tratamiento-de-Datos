## MEMORIA PROYECTO FINAL DATOS: Análisis de la Polarización Ideológica en Redes Sociales a partir de la Propagación de Contenidos Desinformativos
## Noelia de Íscar, Elia Debeni y Alejandra Iturbe
# 1. Introducción
El objetivo central de este proyecto es analizar la relación entre la difusión de desinformación y la polarización ideológica en comunidades sociales. La hipótesis es que ciertos patrones de desinformación actúan como catalizadores de polarización. En este proyecto estudiamos cómo la desinformación se relaciona con la polarización ideológica. Aunque el dataset LIAR no proviene de redes sociales, sí recoge afirmaciones políticas verificadas y metadatos ideológicos (partido, cargo, historial de falsedades). Esto permite modelar cómo los patrones de desinformación pueden estar condicionados por la identidad política del emisor, replicando dinámicas que normalmente se observan también en redes sociales.

# 2. Análisis exploratorio del conjunto de datos

El proyecto utiliza el dataset LIAR, un conjunto ampliamente empleado para tareas de verificación automática de afirmaciones políticas. El dataset contiene 12.836 instancias, cada una correspondiente a una afirmación realizada por un político o figura pública estadounidense.

Cada instancia incluye:
Texto de la afirmación
Etiqueta de veracidad original en 6 niveles. 
Metadatos del emisor: speaker (nombre), party (partido político), state (estado asociado) y speaker_job (cargo laboral).
Historial de credibilidad: conteo de cuantas veces el orador ha emitido afirmaciones clasificadas en cada categoría de veracidad.
Aunque el dataset LIAR se utiliza habitualmente para clasificación (true / false), en este proyecto reinterpretamos la veracidad como una variable continua, asignando valores numéricos a las etiquetas:
True - valor 5 - la declaración es precisa
Mostly-true - valor 4 - la declaración contiene un elemento de verdad, pero puede faltarle contexto
Half-true - valor 3 - la declaración es parcialmente verdadera y parcialmente falsa
Barely-true - valor 2 - la declaración NO es precisa
Pants-fire - valor 0 - la declaración es MUY falsa
Dificultades con este dataset:
Clasificación/Regresión con resultados precisos: es muy difícil para un modelo distinguir, por ejemplo, entre barely-true (2) y half-true (3), ya que la diferencia se basa en la interpretación que hace el verificador.

# 2.1. Estadísticas básicas
Se calcularon estadísticas descriptivas para las variables numéricas relacionadas con el historial de veracidad del orador. Los valores promedio de conteos históricos muestran que, en general, la mayoría de los oradores tienen pocos registros de afirmaciones falsas o parciales, aunque existen casos extremos con hasta 160 afirmaciones categorizadas como “half-true” o 114 como “false”. Esto indica una alta dispersión y asimetría en la distribución de los conteos, lo que puede reflejar la polarización en la actividad de ciertos oradores.
La clase más frecuente es half-true, seguida de false y mostly-true. Los oradores con más conteos de afirmaciones falsas tienden a estar concentrados en rangos bajos, aunque hay outliers con valores muy altos. La distribución de los conteos presenta asimetría positiva, con muchos oradores con pocos registros y unos pocos con números elevados.
# 2.2 Visualizaciones
Se realizaron varias visualizaciones para explorar las propiedades del texto y del target:
Distribución del target: un histograma muestra que las clases altas tienen mayor representación, mientras que las clases bajas, sobre todo 0, tienen menor representación. 
Longitud de los textos: se analiza el número de palabras por afirmación mediante un histograma y un boxplot. Se observa que la mayoría de los textos se encuentran entre 10 y 20 palabras, con algunas afirmaciones más extensas que superan las 40 palabras. El boxplot por target muestra que las clases con mayor falsedad tienden a tener afirmaciones ligeramente más cortas.
Nube de palabras por target: para cada categoría numérica, se genera una nube de palabras que destaca los términos más frecuentes. Esto revela patrones de vocabulario característicos de cada nivel de veracidad, por ejemplo, palabras políticas como "Obama", "state" o "Republican".
Palabras más frecuentes: se calculó la frecuencia de los términos más comunes por target, visualizándolos en gráficos de barras. Esto permite identificar palabras clave y temas recurrentes asociados con cada nivel de veracidad. Se eliminaron palabras como “the”, “in” etc . Algunas de las más comunes son obama, percent, state.
Estas visualizaciones ofrecen una visión intuitiva de cómo se distribuyen las afirmaciones, cuáles son las palabras más representativas y cómo varía la longitud de los textos según la credibilidad asignada.
# 2.3 Hipótesis iniciales
La polarización política incrementa la producción de desinformación y los partidos principales (republicanos y demócratas) mostrarán ratios muy superiores a otros grupos, lo que indica que los incentivos electorales pueden estar impulsando comportamientos comunicativos más agresivos y menos veraces.
Las palabras más frecuentes en las clases extremas estarán asociadas a temas políticos altamente recurrentes ya que estos tópicos aparecen con mayor frecuencia en discursos y declaraciones políticas que suelen ser verificadas.
Al tratar el tema como un problema de regresión prediciendo un valor continuo entre 0 y 5, el rendimiento será inferior respecto a un enfoque de clasificación multiclase ya que las etiquetas representan categorías discretas sin una verdadera distancia numérica entre ellas. Sin embargo este enfoque resulta igualmente interesante porque permite analizar la veracidad como un valor continuo y no sólo como categorías aisladas.
# 3. Representación vectorial del texto
En este proyecto se han comparado tres enfoques diferentes para transformar los documentos en vectores numéricos: TF-IDF, Word2Vec y embeddings contextuales basados en Transformer (BERT). Cada método captura distintos niveles de información lingüística y presenta ventajas y limitaciones que influyen en el rendimiento del modelo.
# 3.1 TF-IDF
TF-IDF representa cada documento como un vector de pesos asociados a términos individuales Combina la frecuencia del término dentro del documento con una penalización a términos muy frecuentes en la colección.  En este trabajo se emplearon unigramas y bigramas, limitando el vocabulario a 10.000 términos y filtrando palabras demasiado raras o demasiado frecuentes.
Esta técnica genera vectores dispersos y de alta dimensionalidad, pero resulta eficaz cuando el contenido depende de palabras clave o patrones léxicos claros.
# 3.2. Word2Vec
Word2Vec aprende representaciones densas de palabras basadas en su contexto. 
En lugar de trabajar con frecuencias, captura similitudes semánticas entre términos. Para representar cada documento se utilizó el promedio de los embeddings de sus palabras.
Sus ventajas principales es que captura relaciones semánticas entre palabras (similaridad, analogías…) y produce vectores densos y de baja dimensión. Sin embargo, pierde el contexto y el orden y dos frases opuestas pueden tener prácticamente el mismo embedding promedio.
# 3.3 Embeddings contextuales (Transformer/BERT)
El tercer enfoque utiliza un modelo Transformer que genera embeddings contextuales, es decir, vectores cuyo significado depende del contexto completo del documento.
Esta representación captura relaciones sintácticas y semánticas profundas y suele superar a los métodos anteriores en la mayoría de tareas de NLP.
# 4. Modelado y evaluación
# 4.1. Red neuronal implementada en PyTorch
# 4.1.1 TF-IDF + Red Neuronal
El rendimiento de la Red Neuronal se mantuvo ligeramente inferior al del modelo lineal base. Esto confirma que, para representaciones basadas en frecuencia de términos (TF-IDF) en datasets pequeños, los modelos lineales son más eficientes, ya que la relación entre la frecuencia de palabras y la veracidad tiende a ser lineal y no requiere la complejidad de capas ocultas no lineales, las cuales introducen una varianza innecesaria.


# 4.1.2 Word2Vec + Red Neuronal
La implementación de la Red Neuronal sobre embeddings de Word2Vec tiene una mejora respecto al enfoque con TF-IDF, logrando revertir la tendencia de sobreajuste y alcanzar la convergencia. A diferencia de la alta dimensionalidad dispersa de TF-IDF, la naturaleza densa y continua de los vectores de Word2Vec (300 dimensiones) permitió a la red neuronal estabilizar el aprendizaje, manteniendo la pérdida de validación (2.32) controlada respecto a la de entrenamiento (2.01). Si bien el rendimiento predictivo sigue siendo inferior al modelo lineal base (Ridge), este resultado valida la hipótesis de que las redes neuronales requieren representaciones densas para extraer patrones en este dataset.

# 4.1.3  BERT embeddings + Red Neuronal
El modelo de Red Neuronal alimentado con embeddings estáticos de BERT obtuvo un rendimiento modesto. Si bien el uso de representaciones semánticas densas permitió una mayor estabilidad en el entrenamiento comparado con TF-IDF (evitando el colapso por sobreajuste), el modelo no logró superar al algoritmo lineal base (Ridge). La activación del Early Stopping en una etapa temprana (época 5) indica que la red saturó rápidamente la información disponible en los embeddings genéricos. Esto demuestra que, al utilizar BERT únicamente como extractor de características fijas sin reentrenar sus pesos, la arquitectura no lineal (MLP) no aporta una ventaja significativa sobre la regresión lineal, evidenciando que la complejidad del problema está en matices contextuales finos que requieren un Fine-Tuning completo del modelo Transformer para ser capturados eficazmente

CONCLUSIÓN REDES NEURONALES: las redes neuronales alimenatadas con datos fijos (estáticos) no logran superar el umbral de 0.05-0.07 que marca el modelo lineal (Ridge). Necesitamos fine tuning.
# 4.2. Modelos de Scikit-learn
Para establecer una línea base en la predicción del nivel de desinformación, se entrenaron modelos clásicos de Scikit-learn utilizando las distintas representaciones vectoriales del texto. El objetivo es analizar cómo se comportan técnicas tradicionales de regresión cuando se alimentan con los tres tipos de embeddings explicados anteriormente. 
Para preparar los datos el conjunto se dividió en entrenamiento y test, manteniendo la distribución original de la variable objetivo. 


