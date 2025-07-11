# Curso R No Muerde, Sesión 2

# **Importación y Manipulación de Datos**

## **1. Introducción a la Sesión**

¡Bienvenidos a la segunda sesión del curso de R no muerde! Hoy aprenderemos a instalar librerías, a trabajar con conjuntos de datos públicos, y a manipular data frames usando `dplyr`, una herramienta muy poderosa para manejar datos de manera eficiente en R.

### **Objetivos de la Sesión**

- Aprender a instalar y cargar librerías en R.
- Conocer y explorar conjuntos de datos públicos.
- Utilizar `dplyr` para seleccionar columnas, filtrar datos y crear nuevas columnas.

---

## **2. Instalación y Carga de Librerías**

### **¿Qué es una librería en R?**

Una librería en R es un conjunto de funciones, datos y otros recursos que nos ayudan a realizar tareas específicas. Por ejemplo, `dplyr` es una librería que facilita la manipulación de datos.

### **Cómo instalar una librería**

Para instalar una librería en R, usaremos el siguiente comando. Aquí instalaremos `tidyverse`, que incluye `dplyr` y otras herramientas útiles:

```r
install.packages("tidyverse") # es probable que te pida seleccionar algún lugar de descarga. (Chile, https)
```

### **Cargar una librería en R**

Una vez instalada, necesitamos "cargar" la librería para poder usarla:

```r
library(tidyverse)
```

¡Y ya estamos listos para usar `dplyr` y otras funciones del `tidyverse`!

---

## **3. Conjuntos de Datos Públicos en R**

R incluye algunos conjuntos de datos públicos que podemos usar para practicar. Hoy trabajaremos con dos de ellos: `mtcars` e `iris`.

### **3.1. Exploración de los datos**

Podemos ver las primeras filas de estos conjuntos de datos con el comando `head()`:

```r
head(mtcars)
head(iris)
```

Esto nos mostrará un resumen de las primeras filas de cada conjunto de datos, para que podamos entender mejor su estructura.

### **3.2. ¿Qué contienen estos datos?**

Para obtener más información sobre los datos, podemos usar:

```r
?mtcars
?iris
```

Este comando nos mostrará una descripción de las columnas y el contenido de cada conjunto de datos.

---

## **4. Manipulación de Data Frames**

En esta sección, aprenderemos a manipular `data frames` utilizando funciones nativas de R y  el paquete `dplyr`, que es una herramienta poderosa y fácil de usar para la manipulación de datos en R. Antes de sumergirnos en `dplyr`, también cubriremos cómo utilizar las funciones `colnames()` y `rownames()` para trabajar con los nombres de columnas y filas, y cómo establecer y obtener el directorio de trabajo utilizando `getwd()` y `setwd()`.

### **4.1 Establecer y Obtener el Directorio de Trabajo**

Antes de comenzar a manipular datos, es importante saber cómo manejar el directorio de trabajo en R. El directorio de trabajo es la carpeta donde R busca archivos para leer y donde guarda los archivos que exportas.

- **Obtener el Directorio de Trabajo Actual:**
    
    ```r
    getwd()
    ```
    
    - **Explicación:** `getwd()` devuelve la ruta del directorio de trabajo actual. Esto es útil para verificar dónde estás trabajando dentro de tu sistema de archivos.
- **Establecer un Nuevo Directorio de Trabajo:**
    
    ```r
    setwd("/ruta/a/tu/directorio")
    ```
    
    - **Explicación:** `setwd("/ruta/a/tu/directorio")` cambia el directorio de trabajo a la ruta especificada. Asegúrate de utilizar la barra diagonal correcta (`/` para macOS y Linux, y puede ser `\\` o `/` para Windows).

### **4.2 Trabajando con Nombres de Columnas y Filas**

- **Obtener y Establecer Nombres de Columnas:**
    - **Obtener los nombres de las columnas de un `data frame`:**
        
        ```r
        colnames(iris)
        ```
        
        - **Explicación:** `colnames(`colnames(iris)`)` devuelve un vector con los nombres de las columnas del `data frame`.
    - **Establecer los nombres de las columnas:**
        
        ```r
        iris2 <- iris # duplicamos el dataframe "iris"
        
        colnames(iris2) <- c("Largo.Sepalo", "Ancho.Sepalo", "Largo.Petalo", "Ancho.Petalo", "Especie")
        colnames(iris2)
        head(iris2)
        ```
        
        - **Explicación:** Asigna nuevos nombres a las columnas del `data frame`. Asegúrate de que el número de nombres coincida con el número de columnas.
- **Obtener y Establecer Nombres de Filas:**
    - **Obtener los nombres de las filas de un `data frame`:**
        
        ```r
        rownames(iris)
        ```
        
        - **Explicación:** `rownames(mi_data_frame)` devuelve un vector con los nombres de las filas del `data frame`.
    - **Establecer los nombres de las filas:**
        
        ```r
        rownames(iris2) <- c("Fila1", "Fila2", "Fila3") # pero les saldrá un error ya que no coincide con el número de filas
        ```
        
        - **Explicación:** Asigna nuevos nombres a las filas del `data frame`.
- **Creación de un histograma simple:**
    - Los histogramas son útiles para ver la distribución de tus datos y para identificar patrones, como si hay más datos concentrados en un rango específico o si los datos están distribuidos de manera uniforme:
    
    ```r
    hist(iris2$Largo.Sepalo)
    hist(iris2$Largo.Sepalo,nclass=20)
    ```
    
    - **Explicación:** `hist()` permite crear de manera rápida un histograma con los datos de una columna específica. Recuerda utilizar `$` para seleccionar columnas específicas de tu Data Frame.

### **4.3 Manipulación de Data Frames con `dplyr`**

`dplyr` es un paquete que proporciona un conjunto de funciones que permiten manipular `data frames` de manera eficiente y legible. Las operaciones más comunes incluyen seleccionar columnas, filtrar filas, ordenar datos, mutar (crear) nuevas columnas, y resumir datos.

### **4.3.1 Instalar y Cargar `dplyr`**

- **Instalar `dplyr`:**
    
    ```r
    install.packages("dplyr")
    ```
    
- **Cargar `dplyr`:**
    
    ```r
    library(dplyr)
    ```
    

### **4.3.2 Operaciones Básicas con `dplyr`**

- **Seleccionar Columnas (`select`):**
    
    ```r
    iris_seleccionado <- select (iris, Sepal.Length, Species)
    ```
    
    - **Explicación:** `select()` te permite elegir solo las columnas que necesitas.
- **También puedes seleccionar las mismas columnas con funciones nativas de R :**
    
    ```r
    iris_seleccionado2 <- iris[, c(1,5)]
    iris_seleccionado3 <- data.frame(iris$Sepal.Length,iris$Species)
    ```
    
    - **Explicación: E**stas 2 instrucciones llegan a lo mismo, donde se permite elegir solo las columnas que necesitas.
- **Filtrar Filas (`filter`):**
    
    ```r
    iris_filtrado <- filter(iris, Sepal.Length > 7)
    ```
    
    - **Explicación:** `filter()` selecciona filas que cumplen con una condición específica.
- **Filtrar Filas (nativo):**
    
    ```r
    iris_filtrado2 <- iris[iris[, 1] > 7, ]
    iris_filtrado3 <- iris[iris$Sepal.Length > 7, ]
    ```
    
    - **Explicación:** Funciones nativas de R para seleccionar filas que cumplen con una condición específica.
- **Ordenar Filas (`arrange`):**
    
    ```r
    iris_ordenado <- arrange(iris, Sepal.Length)
    ```
    
    - **Explicación:** `arrange()` ordena las filas de acuerdo con los valores de una o más columnas.
- **Ordenar Filas (nativo):**
    
    ```r
    iris_ordenado2 <- iris[order(iris[, 1]), ]
    iris_ordenado3 <- iris[order(iris$Sepal.Length), ]
    ```
    
    - **Explicación:** `arrange()` ordena las filas de acuerdo con los valores de una o más columnas.
- **Mutar o Crear Nuevas Columnas (`mutate`):**
    
    ```r
    iris_mutado <- mutate(iris, NuevaColumna = Sepal.Length * 2)
    
    iris_mutado2 <- cbind(iris, NuevaColumna = iris$Sepal.Length * 2) # funcion nativa
    ```
    
    - **Explicación:** `mutate()` crea nuevas columnas basadas en transformaciones de las columnas existentes.
- **Resumir Datos (`summarize` y `group_by`):**
    
    ```r
    iris_resumido <- iris %>%
      group_by(Species) %>%
      summarize(Media.Sepal.Length = mean(Sepal.Length))
    ```
    
    - **Explicación:** `summarize()` se utiliza para calcular estadísticas resumidas, como la media, la suma, etc. `group_by()` agrupa los datos por una o más columnas.

### **4.4. Combinar múltiples funciones con pipes (`%>%`)**

Podemos hacer todo esto de manera más fluida utilizando el operador `%>%`, que se lee como "y luego":

```r
iris_nuevo2 <- iris %>%
  filter(Sepal.Width > 3) %>%
  select(Sepal.Length, Sepal.Width, Petal.Length, Petal.Width) %>%
  mutate(Sepal.ratio = Sepal.Length / Sepal.Width) %>%
  mutate(Petal.ratio = Petal.Length / Petal.Width)

```

Este código selecciona las plantas 3 de ancho de sépalo, luego crea nuevas columnas.

---

## **6. Creación de un Boxplot y Otros Gráficos con `ggplot2`**

### **6.1 Exploración de los Datos**

Antes de crear cualquier gráfico, es importante explorar los datos para entender qué estamos visualizando.

```r
# Mostrar las primeras filas del conjunto de datos iris
head(iris)
```

- **Explicación:** `head(iris)` muestra las primeras seis filas del conjunto de datos `iris`. Esto nos permite echar un vistazo a las variables que contiene, como `Species`, `Sepal.Length`, etc.

### **6.2 Creación de un Barplot**

Comenzaremos creando un gráfico de barras para visualizar la longitud media del sépalo (`Sepal.Length`) para cada especie.

```r
# Crear un gráfico de barras
p_iris_bar <- ggplot(iris, aes(x = Species, y = Sepal.Length)) +
  geom_bar(stat = "summary", fun = "mean")
p_iris_bar
```

- **Explicación:**
    - `ggplot(iris, aes(x = Species, y = Sepal.Length))`: Especifica el conjunto de datos `iris` y mapea `Species` al eje x y `Sepal.Length` al eje y.
    - `geom_bar(stat = "summary", fun = "mean")`: Crea un gráfico de barras donde la altura de cada barra es la media de `Sepal.Length` para cada `Species`.
    - `p_iris_bar`: Imprime el gráfico de barras.

### **6.3 Creación de un Boxplot**

Ahora, crearemos un boxplot para visualizar la distribución de `Sepal.Length` por especie.

```r
# Crear un boxplot
p_iris_box <- ggplot(iris, aes(x = Species, y = Sepal.Length)) +
  geom_boxplot()
p_iris_box
```

- **Explicación:**
    - `geom_boxplot()`: Añade un boxplot al gráfico, que muestra la mediana, el rango intercuartil (IQR) y posibles valores atípicos de `Sepal.Length` para cada `Species`.

### **6.4 Creación de un Violin Plot**

El violin plot combina aspectos del boxplot y la densidad de kernel.

```r
# Crear un violin plot
p_iris_violin <- ggplot(iris, aes(x = Species, y = Sepal.Length)) +
  geom_violin()
p_iris_violin
```

- **Explicación:**
    - `geom_violin()`: Crea un gráfico de violín, que muestra la distribución de los datos de manera similar a un boxplot pero con una forma que representa la densidad de los datos.

### **6.5 Creación de un Density Plot**

Un density plot es útil para visualizar la distribución de una variable continua.

```r
# Crear un gráfico de densidad
p_iris_density <- ggplot(iris, aes(x = Sepal.Length, color = Species, fill = Species)) +
  geom_density(alpha = 0.4)
p_iris_density
```

- **Explicación:**
    - `geom_density(alpha = 0.4)`: Añade una capa de densidad al gráfico, con una transparencia (`alpha`) del 40%. Esto permite ver la superposición de densidades entre las diferentes especies.

### **6.6 Creación de un Scatter Plot con una Línea de Regresión**

Un scatter plot (gráfico de dispersión) es útil para visualizar la relación entre dos variables continuas. Aquí añadiremos también una línea de regresión lineal para mostrar la tendencia en los datos.

```r
# Crear un scatter plot con una línea de regresión
p_iris_scatter <- ggplot(iris, aes(x = Sepal.Length, y = Petal.Length)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE)
p_iris_scatter

```

- **Explicación:**
    - `ggplot(iris, aes(x = Sepal.Length, y = Petal.Length))`: Mapeamos `Sepal.Length` al eje X y `Petal.Length` al eje Y para visualizar la relación entre estas dos variables.
    - `geom_point()`: Añade los puntos al gráfico, donde cada punto representa una observación del conjunto de datos.
    - `geom_smooth(method = "lm", se = FALSE)`: Añade una línea de regresión lineal (`method = "lm"`), sin mostrar la banda de error (`se = FALSE`).

Este scatter plot muestra visualmente si hay una relación lineal entre la longitud del sépalo y la longitud del pétalo. Es una manera efectiva de ver la correlación entre dos variables en un gráfico.

### **6.7 Personalización del Boxplot**

A continuación, personalizaremos el boxplot para que tenga un aspecto más atractivo y diferenciado por colores.

```r
# Crear un boxplot personalizado
p_iris <- ggplot(iris, aes(x = Species, y = Sepal.Length, fill = Species)) +
  geom_boxplot(aes(fill = Species)) +
  scale_fill_manual(values = c("darkred", "darkblue", "darkgreen")) +
  theme_bw()
p_iris
```

- **Explicación:**
    - `aes(fill = Species)`: Colorea los boxplots según la especie.
    - `scale_fill_manual(values = c("darkred", "darkblue", "darkgreen"))`: Asigna colores específicos a cada especie.
    - `theme_bw()`: Aplica un tema de fondo blanco y limpio al gráfico.

### **6.8 Creación de un Boxplot con Error Bars**

Finalmente, añadimos barras de error al boxplot para visualizar el rango intercuartil.

```r
# Crear un boxplot con barras de error
p_iris <- ggplot(iris, aes(x = Species, y = Sepal.Length, fill = Species)) +
  stat_boxplot(geom = "errorbar") +
  geom_boxplot(aes(fill = Species)) +
  scale_fill_manual(values = c("darkred", "darkblue", "darkgreen")) +
  theme_bw()
p_iris

# Guardar el plot en la carpeta de tu working directory (wd)
getwd()
pdf("iris.boxplot.pdf")
p_iris
dev.off()

# También lo puedes guardar con los comandos del paquete ggplot2
ggsave("iris.boxplot2.pdf", plot = p_iris) 
```

- **Explicación:**
    - `stat_boxplot(geom = "errorbar")`: Añade barras de error a cada boxplot para mostrar la extensión del rango intercuartil.
    - El resto del código es similar al boxplot personalizado anterior.

---

Este conjunto de ejemplos proporciona una introducción sólida a la creación de diferentes tipos de gráficos utilizando `ggplot2` en R. Cada gráfico ofrece una perspectiva diferente de los datos, lo que es útil para diversas tareas de análisis y visualización.

---

## **7. Ejercicios Prácticos**

### **7.1. Ejercicio 1: Seleccionar y filtrar datos**

- **Tarea:** Usando el conjunto de datos `iris`, selecciona las columnas `Sepal.Length`, `Sepal.Width` y `Species`, y filtra para mostrar solo las filas donde `Sepal.Width` sea mayor a 3.

### **7.2. Ejercicio 2: Crear nuevas columnas**

- **Tarea:** En el conjunto de datos `mtcars`, crea una nueva columna llamada `efficiency` que sea el resultado de dividir `mpg` por `wt`.

### **7.3. Ejercicio 3: Crear un gráfico simple**

- **Tarea:** Usando el conjunto de datos `mtcars`, crea un gráfico de dispersión (`scatter plot`) que muestre la relación entre `hp` (caballos de fuerza) y `mpg` (millas por galón). Utiliza la función `ggplot()` para hacerlo.