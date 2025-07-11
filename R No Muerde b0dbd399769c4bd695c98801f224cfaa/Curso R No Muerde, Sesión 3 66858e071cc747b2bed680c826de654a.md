# Curso R No Muerde, Sesión 3

# **Guía para Análisis de Datos en Plantas**

### Objetivos:

- Crear un conjunto de datos ficticio.
- Visualizar datos con gráficos en R usando **ggplot2**.
- Aplicar pruebas estadísticas (ANOVA, Kruskal-Wallis, t-test).
- Interpretar los resultados.

Análisis de datos con R: Comparación entre tratamiento de sequía y control

En este tutorial vamos a aprender cómo comparar dos grupos de datos (por ejemplo, plantas bajo tratamiento de sequía versus plantas en condiciones normales) usando R. También veremos cómo decidir si utilizamos una prueba t o una prueba no paramétrica. 

### Paso 1: Preparar el entorno de trabajo

Primero, necesitamos cargar las bibliotecas necesarias. Las bibliotecas son como "cajas de herramientas" que nos ayudan a hacer diferentes cosas en R. En este caso, vamos a usar algunas para manejar y visualizar datos.

```r
# Instalamos las bibliotecas si no las tenemos ya instaladas
if (!require("ggplot2")) install.packages("ggplot2")
if (!require("dplyr")) install.packages("dplyr")
if (!require("ggpubr")) install.packages("ggpubr")

# Cargar las bibliotecas
library(ggplot2) # Para hacer gráficos
library(dplyr)   # Para manipular datos
library(ggpubr)  # Para gráficos adicionales

```

### Paso 2: Crear datos ficticios

Como no tenemos datos reales, vamos a crear algunos datos ficticios. Imagina que medimos el peso de 30 plantas bajo condiciones normales (control) y 30 plantas bajo sequía.

```r
# Fijamos la semilla para que los resultados sean reproducibles (esto solo es necesario para este ejemplo)
set.seed(123)

# Generar datos ficticios
control <- rnorm(30, mean = 15, sd = 2)  # 30 plantas en control, promedio de 15 gramos
sequía <- rnorm(30, mean = 10, sd = 2)   # 30 plantas en sequía, promedio de 10 gramos

# Crear un data frame (una tabla de datos) con los datos
datos <- data.frame(
  grupo = rep(c("Control", "Sequía"), each = 30),
  peso = c(control, sequía)
)

# Mostrar las primeras filas de la tabla de datos
head(datos)

```

### Paso 3: Visualizar los datos

Antes de hacer cualquier análisis, es una buena idea visualizar los datos para ver cómo se ven. Vamos a hacer un gráfico de caja (boxplot).

```r
# Crear un boxplot con puntos individuales y cajas más transparentes usando ggboxplot
ggboxplot(datos, x = "grupo", y = "peso", 
          fill = "grupo", # Relleno del boxplot según el grupo
          palette = c("#00AFBB", "#E7B800"), # Colores para cada grupo
          alpha = 0.5, # Nivel de transparencia de las cajas (0 = completamente transparente, 1 = opaco)
          add = "jitter", # Agregar puntos individuales con un ligero desplazamiento
          add.params = list(color = "grupo", size = 1.5, jitter = 0.2), # Color de los puntos según grupo
          ylab = "Peso de la planta (g)", xlab = "Condición",
          title = "Boxplot con puntos individuales: Condición Control vs Sequía") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5)) # Centrar el título

```

### Paso 4: Evaluar la normalidad de los datos

Para decidir qué prueba estadística usar, primero necesitamos saber si nuestros datos siguen una distribución normal. Esto lo hacemos usando la prueba de Shapiro-Wilk.

```r
# Evaluar la normalidad de los datos para cada grupo
shapiro_control <- shapiro.test(datos$peso[datos$grupo == "Control"]) # Prueba de normalidad para el grupo control
shapiro_sequia <- shapiro.test(datos$peso[datos$grupo == "Sequía"])   # Prueba de normalidad para el grupo sequía

# Mostrar resultados de la prueba de normalidad
shapiro_control
shapiro_sequia

```

- **Nota:** Si el valor p (p-value) es mayor que 0.05, significa que los datos son normales. Si es menor que 0.05, significa que los datos no son normales.

Para visualizar la distribución de los pesos en cada condición (Control y Sequía) utilizando gráficos de densidad, podemos utilizar la función `geom_density()` en lugar de `geom_histogram()`. Los gráficos de densidad son útiles para observar la distribución suavizada de los datos.

Aquí te dejo el código en R para crear los gráficos de densidad superpuestos:

```r
# Crear un gráfico de densidad para ambas condiciones: Control y Sequía

ggplot(datos, aes(x = peso, fill = grupo)) +
  geom_density(alpha = 0.6) + # Agregar gráficos de densidad con transparencia
  scale_fill_manual(values = c("#00AFBB", "#E7B800")) + # Colores para cada grupo
  labs(title = "Distribución de densidad de pesos en condiciones Control y Sequía",
       x = "Peso de la planta (g)", y = "Densidad") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5))

```

### Explicación del código:

1. **`ggplot(datos, aes(x = peso, fill = grupo))`**: Creamos el gráfico con `ggplot`, usando los datos y especificando que en el eje x vamos a poner el `peso`. Los grupos (Control y Sequía) se diferenciarán por colores con `fill = grupo`.
2. **`geom_density(alpha = 0.6)`**: Añadimos los gráficos de densidad con `geom_density()`. El parámetro `alpha = 0.6` controla la transparencia, permitiendo ver los gráficos de densidad superpuestos.
3. **`scale_fill_manual(values = c("#00AFBB", "#E7B800"))`**: Especificamos los colores para cada grupo.
4. **`labs(...)`**: Añadimos títulos y etiquetas a los ejes.
5. **`theme_minimal()` y `theme(plot.title = element_text(hjust = 0.5))`**: Aplicamos un tema minimalista y centramos el título del gráfico.

Este gráfico te permitirá comparar visualmente cómo se distribuyen los pesos de las plantas bajo las condiciones de control y sequía de una manera suavizada, facilitando la identificación de diferencias en la distribución.

### Paso 5: Selección de la prueba estadística

Dependiendo de si los datos son normales o no, elegimos la prueba a realizar:

- **Prueba t de Student:** si los datos son normales.
- **Prueba de Wilcoxon (no paramétrica):** si los datos no son normales.

```r
# Decidir qué prueba usar basándonos en los resultados de normalidad
if(shapiro_control$p.value > 0.05 & shapiro_sequia$p.value > 0.05) {
  # Si ambos grupos tienen datos normales, usamos la prueba t
  resultado_t_test <- t.test(peso ~ grupo, data = datos, alternative = "two.sided")
  print("Los datos son normales. Realizamos una prueba t:")
  print(resultado_t_test)
} else {
  # Si alguno de los grupos no tiene datos normales, usamos la prueba de Wilcoxon
  resultado_wilcoxon <- wilcox.test(peso ~ grupo, data = datos, alternative = "two.sided")
  print("Los datos no son normales. Realizamos una prueba de Wilcoxon:")
  print(resultado_wilcoxon)
}

```

### Paso 6: Interpretación de resultados

- **Para la prueba t:** Si el valor p es menor que 0.05, hay una diferencia significativa entre los dos grupos.
- **Para la prueba de Wilcoxon:** Si el valor p es menor que 0.05, también hay una diferencia significativa.

```r
# Interpretar los resultados
if (exists("resultado_t_test")) {
  # Interpretación para la prueba t
  if (resultado_t_test$p.value < 0.05) {
    print("Existe una diferencia significativa en el peso de las plantas entre las condiciones de sequía y control.")
  } else {
    print("No existe una diferencia significativa en el peso de las plantas entre las condiciones de sequía y control.")
  }
} else if (exists("resultado_wilcoxon")) {
  # Interpretación para la prueba de Wilcoxon
  if (resultado_wilcoxon$p.value < 0.05) {
    print("Existe una diferencia significativa en el peso de las plantas entre las condiciones de sequía y control.")
  } else {
    print("No existe una diferencia significativa en el peso de las plantas entre las condiciones de sequía y control.")
  }
}

```

### Datos no normales

Vamos a aprender cómo generar datos no paramétricos, evaluar su normalidad, realizar un análisis estadístico adecuado y visualizar los resultados. 

---

### **Paso 1: Generar Datos No Paramétricos**

Primero, necesitamos crear datos que no sigan una distribución normal. Para esto, utilizamos una distribución exponencial, que es un tipo de distribución que genera datos sesgados, es decir, no simétricos.

```r
set.seed(123) # Establecer semilla para reproducibilidad

# Generar datos no normales con distribución exponencial
control_no_param <- rexp(30, rate = 0.2)  # 30 muestras para el grupo Control
sequia_no_param <- rexp(30, rate = 0.5)   # 30 muestras para el grupo Sequía

```

- **`set.seed(123)`**: Establecemos una "semilla" para que los resultados sean reproducibles. Esto significa que, si ejecutas el código, obtendrás los mismos valores que yo.
- **`rexp(30, rate = 0.2)`**: Genera 30 números aleatorios de una distribución exponencial con una tasa de 0.2 para el grupo Control. El valor `rate` afecta la forma de la distribución.
- **`rexp(30, rate = 0.5)`**: Genera 30 números aleatorios para el grupo Sequía con una tasa de 0.5, lo que da como resultado una distribución diferente a la del grupo Control.

---

### **Paso 2: Crear un Data Frame con los Datos**

Ahora vamos a organizar estos datos en una estructura de tabla, llamada `data frame`, que es más fácil de manejar en R.

```r
# Crear un data frame con los datos generados
datos_no_param <- data.frame(
  grupo = rep(c("Control", "Sequía"), each = 30),
  peso = c(control_no_param, sequia_no_param)
)

# Mostrar las primeras filas de la tabla de datos
head(datos_no_param)

```

- **`data.frame(...)`**: Crea una tabla con dos columnas:
    - `grupo`: Indica si la muestra pertenece al grupo "Control" o "Sequía".
    - `peso`: Contiene los valores generados anteriormente.
- **`rep(c("Control", "Sequía"), each = 30)`**: Repite las etiquetas "Control" y "Sequía" 30 veces cada una, para que coincidan con los 30 valores de cada grupo.
- **`head(datos_no_param)`**: Muestra las primeras 6 filas de la tabla para verificar que los datos se han creado correctamente.

---

### **Paso 3: Evaluar la Normalidad de los Datos**

Antes de realizar cualquier análisis estadístico, necesitamos saber si nuestros datos siguen una distribución normal. Para esto, utilizamos la prueba de Shapiro-Wilk.

```r
# Prueba de normalidad para cada grupo
shapiro_control_np <- shapiro.test(datos_no_param$peso[datos_no_param$grupo == "Control"])
shapiro_sequia_np <- shapiro.test(datos_no_param$peso[datos_no_param$grupo == "Sequía"])

# Mostrar resultados de la prueba de Shapiro-Wilk
print("Prueba de Shapiro-Wilk para el grupo Control:")
shapiro_control_np
print("Prueba de Shapiro-Wilk para el grupo Sequía:")
shapiro_sequia_np

```

- **`shapiro.test(...)`**: Realiza la prueba de Shapiro-Wilk para evaluar la normalidad de los datos. Si el valor p (p-value) es menor que 0.05, significa que los datos no son normales.
- **`datos_no_param$peso[datos_no_param$grupo == "Control"]`**: Selecciona los valores de peso correspondientes al grupo Control.
- **`print(...)`**: Muestra el resultado de la prueba en la consola. Esto nos ayuda a decidir qué tipo de prueba estadística utilizar.

---

### **Paso 4: Visualización con Boxplot**

Vamos a crear un gráfico de caja o "boxplot" para ver cómo se distribuyen los datos en cada grupo. Además, agregaremos los puntos individuales para ver mejor cada valor.

```r
# Crear un boxplot con puntos individuales
ggboxplot(datos_no_param, x = "grupo", y = "peso",
          fill = "grupo", # Relleno del boxplot según el grupo
          palette = c("#00AFBB", "#E7B800"), # Colores para cada grupo
          alpha = 0.5, # Transparencia de las cajas
          add = "jitter", # Agregar puntos individuales con un ligero desplazamiento
          add.params = list(color = "grupo", size = 1.5, jitter = 0.2), # Ajustes de los puntos
          ylab = "Peso de la planta (g)", xlab = "Condición",
          title = "Boxplot con puntos individuales: Condición Control vs Sequía (Datos no paramétricos)") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5)) # Centrar el título

```

- **`ggboxplot(...)`**: Crea un boxplot, que muestra la mediana, los cuartiles y posibles valores atípicos de los datos.
- **`add = "jitter"`**: Añade los puntos individuales a cada grupo para que sean visibles.
- **`alpha = 0.5`**: Ajusta la transparencia de las cajas del boxplot.
- **`theme_minimal()`**: Aplica un tema minimalista al gráfico.
- **`theme(plot.title = element_text(hjust = 0.5))`**: Centra el título del gráfico.

---

### **Paso 5: Crear un Gráfico de Densidad**

Un gráfico de densidad nos muestra la forma de la distribución de los datos de manera suavizada.

```r
# Crear un gráfico de densidad para ambas condiciones
ggplot(datos_no_param, aes(x = peso, fill = grupo)) +
  geom_density(alpha = 0.6) + # Agregar gráficos de densidad con transparencia
  scale_fill_manual(values = c("#00AFBB", "#E7B800")) + # Colores para cada grupo
  labs(title = "Distribución de densidad de pesos en condiciones Control y Sequía",
       x = "Peso de la planta (g)", y = "Densidad") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5))

```

- **`geom_density(alpha = 0.6)`**: Añade gráficos de densidad con una transparencia del 60%.
- **`scale_fill_manual(values = c("#00AFBB", "#E7B800"))`**: Define los colores de cada grupo (Control y Sequía).
- **`labs(...)`**: Añade el título y las etiquetas para los ejes.

---

### **Paso 6: Realizar la Prueba Estadística Adecuada**

Basado en los resultados de la prueba de Shapiro-Wilk, decidimos si usamos una prueba t (para datos normales) o una prueba de Wilcoxon (para datos no normales).

```r
# Decidir qué prueba usar según la normalidad de los datos
if(shapiro_control_np$p.value > 0.05 & shapiro_sequia_np$p.value > 0.05) {
  # Si ambos grupos son normales, usamos prueba t
  resultado_t_test_np <- t.test(peso ~ grupo, data = datos_no_param, alternative = "two.sided")
  print("Los datos son normales. Realizamos una prueba t:")
  print(resultado_t_test_np)
} else {
  # Si alguno de los grupos no es normal, usamos prueba de Wilcoxon
  resultado_wilcoxon_np <- wilcox.test(peso ~ grupo, data = datos_no_param, alternative = "two.sided")
  print("Los datos no son normales. Realizamos una prueba de Wilcoxon:")
  print(resultado_wilcoxon_np)
}

```

- **`if(...)`**: Compara los valores p de la prueba de Shapiro-Wilk.
- **`t.test(...)`**: Realiza una prueba t si los datos son normales.
- **`wilcox.test(...)`**: Realiza una prueba de Wilcoxon si los datos no son normales.
- **`print(...)`**: Muestra los resultados de la prueba estadística en la consola.

---

### Resumen

Con estos pasos, hemos generado datos no paramétricos, evaluado su normalidad, visualizado sus distribuciones y realizado la prueba estadística adecuada.

# 2 Factores

### 1. **Creación de un Conjunto de Datos Para análisis de 2 factores.**

Vamos a simular un experimento en el que medimos el peso fresco (FW) de rosetas de plantas bajo diferentes tratamientos y genotipos. Crearemos un **data frame** con datos ficticios.

```r
# Creamos un data frame con datos inventados de plantas
set.seed(123)  # Esto nos permite tener los mismos resultados cada vez que corremos el código

# Definir variables
Genotype <- rep(c("WT", "Mutant"), each = 20)
Treatment <- rep(c("Control", "Drought"), times = 10)
FW <- c(rnorm(10, mean = 1.5, sd = 0.2),  # WT bajo Control
        rnorm(10, mean = 0.8, sd = 0.2),  # WT bajo Sequía
        rnorm(10, mean = 1.4, sd = 0.3),  # Mutante bajo Control
        rnorm(10, mean = 0.6, sd = 0.2))  # Mutante bajo Sequía

# Crear el data frame
plant_data <- data.frame(Genotype, Treatment, FW)

# Guardar el data frame en un archivo de texto
write.table(plant_data, file = "plant_data.txt", sep = "\t", row.names = FALSE, col.names = TRUE)
```

Con este código, hemos creado un conjunto de datos con dos genotipos ("WT" y "Mutant") sometidos a dos tratamientos distintos ("Control", "Drought"). El peso fresco de las plantas se generó usando valores aleatorios basados en distribuciones normales.

### 2. **Importar el Conjunto de Datos**

Si les proporcionamos el archivo `plant_data.txt`, podemos enseñarles cómo importarlo en R.

```r
# Leer el archivo con los datos de plantas
plant_data <- read.table("plant_data.txt", header = TRUE, sep = "\t")

# Ver una vista previa de los datos
head(plant_data)
```

### 3. **Visualización de Datos con ggplot2**

Primero, es importante ver cómo se distribuyen los datos. Vamos a crear un gráfico de barras con barras de error para mostrar la media del peso fresco por genotipo y tratamiento.

```r
# Instalar y cargar ggplot2 si no lo tienen instalado
# install.packages("ggplot2")
library(ggplot2)

# Resumir datos para calcular la media y el error estándar
library(dplyr)
summary_data <- plant_data %>%
  group_by(Genotype, Treatment) %>%
  summarise(Mean_FW = mean(FW), SE_FW = sd(FW) / sqrt(n()))

# Crear el gráfico
ggplot(summary_data, aes(x = Treatment, y = Mean_FW, fill = Genotype)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_errorbar(aes(ymin = Mean_FW - SE_FW, ymax = Mean_FW + SE_FW),
                position = position_dodge(0.9), width = 0.2) +
  labs(title = "Peso Fresco Promedio de Rosetas",
       x = "Tratamiento",
       y = "Peso Fresco Promedio (g)") +
  theme_minimal() +
  theme(legend.position = "top") +
  scale_fill_brewer(palette = "Set1")
```

Este gráfico muestra el peso promedio para cada combinación de genotipo y tratamiento, con barras de error que representan el error estándar.

### 4. **Verificación de Supuestos para ANOVA**

Antes de realizar un ANOVA, necesitamos verificar si los datos cumplen con los supuestos de normalidad y homogeneidad de varianzas.

### 4.1. **Normalidad de los residuos**

Primero, ajustamos un modelo de ANOVA y verificamos si los residuos siguen una distribución normal.

```r
# Ajustar modelo ANOVA
anova_model <- aov(FW ~ Genotype * Treatment, data = plant_data)

# Prueba de normalidad (Shapiro-Wilk)
shapiro.test(residuals(anova_model))

```

**Explicación:**

- **`shapiro.test(...)`**: Esta función realiza la prueba de Shapiro-Wilk para verificar la normalidad de los datos.
- **`residuals(anova_model)`**: Aquí estamos extrayendo los residuales del modelo de ANOVA (denotado como `anova_model`). Los residuales son las diferencias entre los valores observados y los valores predichos por el modelo. Estamos evaluando si estos residuales siguen una distribución normal.

**Resultados:**

- Si el valor p (p-value) de la prueba de Shapiro-Wilk es mayor que 0.05, no podemos rechazar la hipótesis nula de que los residuales son normales. Es decir, los residuales pueden considerarse normalmente distribuidos.
- Si el valor p es menor que 0.05, los residuales no siguen una distribución normal, lo cual puede indicar que el modelo no es adecuado o que se necesitan transformaciones adicionales en los datos.

### 4.2. **Homogeneidad de varianzas**

Luego, usamos la prueba de Levene para verificar si las varianzas son homogéneas.

```r
library(car)
leveneTest(FW ~ Genotype * Treatment, data = plant_data)
```

### 

**Explicación:**

- **`leveneTest(...)`**: Realiza la prueba de Levene para verificar la homogeneidad de varianzas. Esta prueba evalúa si las varianzas de los diferentes grupos son iguales, lo cual es un supuesto importante en análisis como ANOVA.
- **`FW ~ Genotype * Treatment`**: Especifica el modelo a evaluar. Aquí se desglosa de la siguiente manera:
    - **`FW`**: Es la variable dependiente o respuesta, que en este caso podría representar el peso fresco (Fresh Weight) de las plantas.
    - **`Genotype`**: Es una variable independiente o factor que representa los diferentes genotipos de las plantas.
    - **`Treatment`**: Es otra variable independiente o factor que representa los diferentes tratamientos aplicados (por ejemplo, control, sequía, etc.).
    - **`Genotype * Treatment`**: El asterisco (``) indica que queremos probar tanto los efectos principales de `Genotype` y `Treatment` como su interacción (cómo el efecto de un factor varía según el otro).
- **`data = plant_data`**: Especifica el conjunto de datos que contiene las variables a analizar. En este caso, `plant_data` debe ser un `data frame` que incluya las columnas `FW`, `Genotype` y `Treatment`.

### **4.3. Interpretación de la Prueba de Levene**

La prueba de Levene evalúa la hipótesis nula de que las varianzas de los grupos son iguales. Se basa en una transformación de los datos que puede manejar casos donde los datos no sean normales, siendo más robusta que otras pruebas como la de Bartlett.

- **Valor p (p-value)**:
    - Si el valor p es **mayor que 0.05**, no podemos rechazar la hipótesis nula, lo que sugiere que las varianzas son homogéneas (iguales).
    - Si el valor p es **menor que 0.05**, se rechaza la hipótesis nula, indicando que las varianzas son significativamente diferentes entre los grupos, lo cual viola el supuesto de homogeneidad de varianzas.

### ¿Por qué es importante?

En análisis como el ANOVA, uno de los supuestos clave es que las varianzas de los grupos comparados sean homogéneas. La prueba de Levene es una herramienta útil para comprobar este supuesto. Si las varianzas no son homogéneas, los resultados del ANOVA pueden no ser válidos, y se deben considerar alternativas como el ANOVA de Welch o pruebas no paramétricas.

### 5. **ANOVA: Análisis de Varianza**

Si los supuestos se cumplen, procedemos con el ANOVA para evaluar si hay diferencias significativas en el peso fresco entre los tratamientos y genotipos.

```r
# Realizar el ANOVA
anova_results <- summary(anova_model)
print(anova_results)
```

### 6. **Pruebas Post hoc (si ANOVA es significativo)**

Si el ANOVA es significativo, realizamos comparaciones por pares con el test de Tukey.

```r
# Test de Tukey para comparaciones por pares
tukey_results <- TukeyHSD(anova_model)
print(tukey_results)

# Gráfico del test de Tukey
plot(tukey_results)
```