# Detección de Fragmentos en Imágenes con OpenCV

Este proyecto permite identificar y clasificar fragmentos en imágenes, como microplásticos, pellets y alquitrán, utilizando la librería OpenCV para el procesamiento de imágenes. A través de una combinación de técnicas de umbralización adaptativa, operaciones morfológicas y clasificación basada en características geométricas y de color, el programa detecta y separa distintos tipos de fragmentos.

## Descripción del Funcionamiento

### Objetivo

El objetivo de este programa es detectar fragmentos en imágenes, clasificarlos según su tipo (pellets, fragmentos de microplásticos y alquitrán), y mostrar los resultados de detección en la imagen original. El proceso sigue estos pasos:

1. **Carga de la imagen**: Se carga la imagen que contiene los fragmentos a detectar.
2. **Preprocesamiento de la imagen**:
   - Conversión de la imagen a escala de grises.
   - Aplicación de un suavizado con un filtro Gaussiano para reducir el ruido y mejorar la detección de contornos.
   - Umbralización adaptativa gaussiana para segmentar los fragmentos de la imagen.
3. **Detección y filtrado de contornos**: Se detectan los contornos de los fragmentos y se filtran aquellos demasiado pequeños o cercanos.
4. **Clasificación de fragmentos**: Se clasifican los fragmentos en tres categorías: pellets, fragmentos de microplásticos y alquitrán, basándose en su área, circularidad y color.
5. **Resultados**: Se visualiza la imagen con los fragmentos detectados, coloreados según su clasificación.

### Pasos del Algoritmo

1. **Carga y conversión de imagen**:
   ```python
   imagen = cv2.imread('imagen.jpg')
   imagen_gray = cv2.cvtColor(imagen, cv2.COLOR_BGR2GRAY)
   ```

2. **Suavizado y umbralización**:
   Se aplica un filtro Gaussiano para suavizar la imagen y luego se realiza una umbralización adaptativa:
   ```python
   imagen_suavizada = cv2.GaussianBlur(imagen_gray, (7, 7), 0)
   imagen_umbral = cv2.adaptiveThreshold(
       imagen_suavizada, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
       cv2.THRESH_BINARY, 11, 1
   )
   ```

3. **Detección de contornos**:
   Después de aplicar operaciones morfológicas para eliminar ruido, se detectan los contornos de los fragmentos:
   ```python
   contornos_gaussian, _ = cv2.findContours(255-imagen_umbral, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
   ```

4. **Clasificación de fragmentos**:
   Los fragmentos detectados se clasifican según su área, circularidad y color. El programa distingue entre:
   - **Pellets**: Fragmentos grandes y redondos.
   - **Fragmentos de microplásticos**: Fragmentos de formas irregulares.
   - **Alquitrán**: Fragmentos oscuros.
   
   ```python
   def clasificar_fragmento(area, circularidad, contorno, colorPixel):
       if isBlack(colorPixel):
           Particulas['Alquitrán'] +=1
       elif circularidad > 0.70 and area > 75:
           Particulas['Pellet'] +=1
       else:
           Particulas['Fragmento de microplástico'] +=1
   ```

5. **Resultados**:
   Los fragmentos detectados se dibujan en la imagen original con diferentes colores, según su clasificación:
   ```python
   cv2.drawContours(imagen_contornos, pellet, -1, (255, 0, 0), 2)
   cv2.drawContours(imagen_contornos, plastic, -1, (0, 255, 0), 2)
   cv2.drawContours(imagen_contornos, alqui, -1, (0, 0, 255), 2)
   ```

### Funciones Principales

- `ResizeWithAspectRatio(image, width=None, height=None)`: Redimensiona la imagen manteniendo la relación de aspecto.
- `distancia_centros(rect1, rect2)`: Calcula la distancia euclidiana entre los centros de dos rectángulos.
- `isBlack(punto)`: Determina si un píxel tiene un color oscuro, clasificándolo como alquitrán.
- `clasificar_fragmento(area, circularidad, contorno, colorPixel)`: Clasifica los fragmentos según su área, circularidad y color.
- `results(real, prediction)`: Calcula métricas de rendimiento como la precisión, el recall y la matriz de confusión.

### Ejemplo de Ejecución

El programa detecta fragmentos en imágenes de prueba y clasifica los fragmentos detectados:

1. **Cargando la imagen**:
   ```python
   imgF = 'fragment-03-olympus-10-01-2020.JPG'
   pruebaSeparado(imgF)
   ```

2. **Mostrando los resultados**:
   La imagen con los fragmentos detectados se muestra en pantalla, y se imprime en consola el número total de fragmentos clasificados en cada categoría (pellets, microplásticos y alquitrán).

3. **Clasificación de fragmentos en conjunto**:
   El programa clasifica fragmentos de una imagen con múltiples tipos de partículas y luego calcula las métricas de clasificación, mostrando la matriz de confusión:
   ```python
   real, predict = pruebaJuntos()
   results(real, predict)
   ```

### Problemas Observados

1. **Fragmentos Solapados**: El algoritmo puede no detectar correctamente los fragmentos que están muy cercanos o solapados.
2. **Variaciones en Iluminación**: Cambios en la iluminación de la imagen pueden afectar la clasificación de los fragmentos, especialmente en el caso de fragmentos de alquitrán.
3. **Diferencias en Tamaño de Fragmentos**: Fragmentos con tamaños similares pueden ser clasificados incorrectamente si las diferencias son mínimas.

### Librerías Utilizadas

- **OpenCV**: Para la manipulación de imágenes y la detección de fragmentos.
- **NumPy**: Para cálculos y manejo de datos de imágenes en matrices.
- **Matplotlib y Seaborn**: Para la visualización de la matriz de confusión y otros resultados gráficos.

### Requisitos

- Python 3.x
- OpenCV (`pip install opencv-python`)
- NumPy (`pip install numpy`)
- Matplotlib (`pip install matplotlib`)
- Seaborn (`pip install seaborn`)