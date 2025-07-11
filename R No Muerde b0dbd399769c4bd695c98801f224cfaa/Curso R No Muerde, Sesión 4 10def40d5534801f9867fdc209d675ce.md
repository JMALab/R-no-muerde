# Curso R No Muerde, Sesión 4

---

# **Sesión 4: Práctica Aplicada de Análisis de Datos y Visualización**

### **Este será una sesión más dinámica para que no sea solo copiar y pegar códigos.**

### **Objetivo:**

- Aplicar de manera práctica lo aprendido en sesiones anteriores a través de ejercicios guiados que combinen manipulación de datos, visualización, y análisis estadístico en un flujo de trabajo coherente.

---

## **Obtención de datos**

En la práctica, los datos se almacenan en formatos como archivos de texto. A continuación, aprenderemos a cargar una tabla en R, donde cada fila representa una réplica y cada columna una condición diferente.

### **1. Leer el archivo de texto en R**

Primero, leeremos un archivo de texto con expresión génica, utilizando `read.table` con el separador adecuado (`sep = "\t"` si es tabulado):

Descargue el siguiente archivo en su carpeta de trabajo:

[expresion_genica.txt](Curso%20R%20No%20Muerde,%20Sesio%CC%81n%204%2010def40d5534801f9867fdc209d675ce/expresion_genica.txt)

```r
# Leer el archivo de texto
expresion_data <- read.table("expresion_genica.txt", header = TRUE, sep = "\t", row.names = 1)

# Mostrar las primeras filas del archivo
head(expresion_data)
```

### **Cálculo de Promedio y Desviación Estándar**

Usamos `apply()` para aplicar funciones a lo largo de filas o columnas.

```r
# Calcular promedio y desviación estándar por gen
promedio_expresion <- apply(expresion_data, 2, mean)
desviacion_expresion <- apply(expresion_data, 2, sd)

# Mostrar resultados
data_summary <- data.frame(Condicion = colnames(expresion_data), Promedio = promedio_expresion, Desviacion = desviacion_expresion)
head(data_summary)
```

---

### **2. Transformar de formato ancho a formato largo**

Dado que cada fila es una replica y cada columna es una muestra, debemos transformar los datos a un formato largo, donde cada fila sea la expresión de un gen en una muestra específica.

```r
library(tidyr)

# Convertir a formato largo
expresion_long <- pivot_longer(expresion_data,
                               cols = everything(),
                               names_to = c("Genotipo", "Condicion"),
                               names_sep = "_",
                               values_to = "Expresion")
```

### **Explicación:**

- `cols = everything()` selecciona todas las columnas que representan muestras.
- `names_to = "sample"` nombra las muestras.
- `values_to = "expresion"` coloca los valores de expresión en una columna llamada `expresion`.

---

### 3. Haz un gráfico y un análisis estadístico basado en los ejemplos previos.

# Ejercicios Prácticos

Descarga el siguiente archivo llamado `data_example` en tu computador, el cual se encuentra en formato texto (.txt):

[data_example.txt](Curso%20R%20No%20Muerde,%20Sesio%CC%81n%204%2010def40d5534801f9867fdc209d675ce/data_example.txt)

Una vez descargada la tabla, completa los siguientes pasos en RStudio:

1. Identifica el espacio en el que se encuentra trabajando tu consola (`getwd`).
2. Define tu directorio o carpeta de trabajo, es decir, donde está guardado el archivo recién descargado (`setwd`).
3. Lee el archivo .txt y guárdalo como un data frame en tu consola. Puedes guardar data frame con el nombre `plant_data`.
4. Verifica que los datos se hayan cargado correctamente (`head/tail`).
5. Revisa como se compone el data frame y el tipo de datos para cada columna (`str`).
6. Cambia el tipo de dato de la columna `Treatment` a factor (`as.factor`).
7. Modifica el tipo de dato de la columna `Leaf_Count` a un valor numérico (`as.numeric`).
8. Utiliza las funciones del package `dplyr` para agrupar los datos por `Treatment`. Calcula la media y desviación estándar utilizando `summarise` de la columna `Leaf_Count` para ambos tratamientos.
9. Crea un gráfico de barras utilizando el paquete `ggplot2` que cuantifique la cantidad de hojas (`Leaf_Count`) existen según cada `Treatment.`
    1. Establece que el color de las barras del gráfico sean `salmon`  para Control y `lightgreen` para Fertilized.
    2. Añade barras de error a las barras creadas en el gráfico, utiliza el valor mínimo y máximo como referencia con un tamaño de 0.1
    3. Modifica el largo del eje y (`scale_y_continuous`).
    4. Define el ancho de las barras del gráfico en un tamaño de 0.2.
    5. Guarda el gráfico creado en formato .pdf en la carpeta establecida con anterioridad como espacio de trabajo.
10. Realiza una prueba estadística para verificar la normalidad de los datos de la columna `Leaf_Count` según los tratamientos (`shapiro test`) y luego define el test estadístico más adecuado dependiendo si son normales o no (`t-Test o Kruskal-Wallis` ).
11. Crea una nueva variable llamada `Efficiency_chlor` que calcule la eficiencia de clorofila por hoja, considera la siguiente fórmula al momento de crear la nueva variable: `Chlorophyll` /`Leaf_Count` .
12. Guarda los resultados en un archivo texto (.txt). El data frame `plant_data` en tu carpeta de trabajo con el nombre `plant_analysis.txt`.
13. Crea un gráfico de caja o boxplot utilizando `ggplot2` para visualizar la distribución de la eficiencia de la clorofila (`Efficiency_chlor`) por tratamiento (`Treatment`), y ajusta los colores de las cajas según el tratamiento.
14. Filtra los datos, extrae las observaciones donde el `Leaf_Count` es mayor a 25, y guárdalas en un nuevo data frame llamado `plant_filtered` y guarda el archivo en formato texto con el nombre `plant_analysis2.txt`.
15. Crea una nueva variable que calcule la eficiencia en términos de biomasa por hoja en porcentaje.