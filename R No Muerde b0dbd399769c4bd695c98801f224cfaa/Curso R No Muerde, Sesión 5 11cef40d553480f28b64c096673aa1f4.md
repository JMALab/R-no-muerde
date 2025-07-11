# Curso R No Muerde, Sesión 5

---

# Creación de gráficos de Heatmap (mapas de calor) utilizando `pheatmap` en R (El código tiene errores, encuentre una solución)

---

## Paso 1: Instalación y carga del paquete

Si aún no tienes instalado el paquete `pheatmap` lo puedes instalar con el siguiente comando:

```r
install.packages("pheatmap)
```

Luego, carga el paquete en tu sesión de R:

```r
library(pheatmap)
```

## Paso 2: Preparación de los datos

Vamos a crear una matriz de datos que contenga solo números positivos para facilitar la visualización:

```r
# Crear una matriz de datos aleatorios positivos
set.seed(123)
data_matrix <- matrix(runif(100, min = 1, max = 100), nrow = 10, ncol = 10)

# Asignar nombres a las filas y columnas
rownames(data_matrix) <- paste("Gen", 1:10, sep = "")
colnames(data_matrix) <- paste("Muestra", 1:10, sep = "")

# Ver la matriz
print(data_matrix)
```

## Paso 3: Creación del Heatmap básico

Con los datos listos podemos generar el heatmap básico sin ninguna personalización:

```r
pheatmap(Data_matrix)
```

### Cambio de la escala de colores

Si quieres ajustar la escala de colores, puedes hacer lo siguiente:

```r
pheatmap(data_matrix, scale = "row")  # Escalado por filas
pheatmap(data_matrix, scale = "col")  # Escalado por columnas
```

Este comando ajusta los valores dentro de cada fila/columna para que tengan una media de 0 y una desviación estándar de 1, lo cual es útil cuando las filas tienen diferentes rangos de valores.

## Paso 4: Personalización del Heatmap

### Cambio de la paleta de colores

Vamos a modificar los colores del heatmap para usar una paleta de colores personalizada:

```r
pheatmap(data_matrix., color = colorRampPalette(c("blue", "white", "red"))(50))
```

## Paso 5: Explicación del Z-score

El z-score es una técnica de normalización que permite estandarizar los datos, transformando los valores en función de cuántas desviaciones estándar se encuentran por encima o por debajo de la media. Esto es útil para comparar datos que tienen diferentes escalas o rangos de valores.

En este caso, aplicaremos el Z-score por **filas** para ver cómo se distribuyen los valores dentro de cada fila (gen).

### Aplicar Z-score y crear un Heatmap

Vamos a aplicar la transformación z-score a los datos de la matriz y generar un nuevo heatmap:

```r
# Función para calcular el Z-score por filas
zscore_data_matrix <- t(apply(data_matrix, 1, function(x) (x - mean(x)) / sd(x)))

# Ver la matriz de Z-scores
print(zscore_data_matrix)

# Crear una paleta de colores que va de verde a blanco a rojo, con blanco en el centro (0)
color_palette <- colorRampPalette(c("green", "white", "red"))(50)

# Crear el heatmap con la paleta de colores personalizada
pheatmap(zscore_data_matrix, 
         color = color_palette, 
         breaks = seq(min(zscore_data_matrix), max(zscore_data_matrix), length.out = 51))  # Ajustar los límites de la escala
```

## Paso 6: Agregar anotaciones a las columnas y filas

Puedes agregar anotaciones a las columnas y filas para dar más contexto al heatmap. Aquí un ejemplo:

```r
# Anotaciones para las columnas
annotation_col <- data.frame(
  Grupo = factor(rep(c("A", "B"), each = 5))
)
rownames(annotation_col) <- colnames(data_matrix)

# Anotaciones para las filas
annotation_row <- data.frame(
  Tipo = factor(rep(c("Tipo1", "Tipo2"), each = 5))
)
rownames(annotation_row) <- rownames(data_matrix)

# Crear el heatmap con anotaciones
pheatmap(zscore_data_matrix,
         annotation_col = annotation_col,
         annotation_row = annotation_row)
```

## Paso 7: Guardar el heatmap

Si deseas guardar el gráfico con las anotaciones y las transformaciones aplicadas, puedes hacerlo así:

```r
pheatmap(zscore_data_matrix,
         annotation_col = annotation_col,
         annotation_row = annotation_row,
         filename = "heatmap_zscore_annotated.png")
```

---

## Paso 8: Tarea

Con los siguientes datos de expresión génica:

[datos.curso.txt.gz](Curso%20R%20No%20Muerde,%20Sesio%CC%81n%205%2011cef40d553480f28b64c096673aa1f4/datos.curso.txt.gz)

1. Haga un heatmap con los datos brutos,  la escala de colores debe ser de amarilla a azul, siendo 0 blanco. 
2. Haga el heatmap usando los datos ajustados por z-score. La escala de colores debe ser de -2 a 2, debe ser de azul a rojo. La anotación de las columnas debe hacerse con el archivo:
    
    [datos.curso.info.txt.gz](Curso%20R%20No%20Muerde,%20Sesio%CC%81n%205%2011cef40d553480f28b64c096673aa1f4/datos.curso.info.txt.gz)
    
3. Elija uno de los genes, haga un boxplot y un gráfico de barra.