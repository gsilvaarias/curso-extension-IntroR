### Lunes, 22 de septiembre, 2023
# Introducción a Análisis en R para Datos Biológicos
# Día 5 - Gráficos :)

## Índice
- [Recursos extra para hacer plots](#recursos)
- [Factores](#factores)
- [Plot](#plot)
- [ggplot](#ggplot)

## Recursos extras para hacer plots: <a name = "recursos"></a>
  - [Site ggplot](https://ggplot2.tidyverse.org/)
  - [Libro de ggplot](https://ggplot2-book.org/)
  - [Plot de mapas con ggplotR](https://forms.gle/ETo6d2pJZZc9Ek6J7): "Visualización de datos" > ejemplo 22-26
  - [Tutorial chévere](https://r.qcbs.ca/workshop03/book-en/index.html)

## Factores: <a name = "factores"></a>
**Factores** son estructures de datos **muy** importantes que estábamos ignorando hasta ahora :astonished:

Los factores se parecen a los datos de caracteres, pero se utilizan para representar **información categórica**. Los factores pueden estar ordenados o no y son una clase importante para el análisis estadístico y para hacer gráficos.

Los factores se almacenan como números enteros y tienen etiquetas únicas asociadas a estos números. Aunque los factores parecen (y a menudo se comportan) como vectores de caracteres, en realidad son enteros y hay que tener cuidado al tratarlos como cadenas.

Una vez creados, los factores sólo pueden contener un conjunto predefinido de valores, conocidos como niveles (*levels*). Por defecto, R siempre ordena los niveles por orden alfabético.

Por ejemplo, hagamos un vector con observaciones por concentración de alguna sustancia:
```
conc <- c("Bajo", "Alto", "Medium", "Bajo", "Medio")
conc
str(conc)
class(conc)
fac_conc <- factor(conc)
fac_conc
str(fac_conc)
class(fac_conc)
levels(fac_conc)
nlevels(fac_conc)
```

A veces, el orden de los factores no importa, otras veces puede querer especificar el orden porque es significativo (por ejemplo, "bajo", "medio", "alto") o porque lo requiere un tipo de análisis concreto. Además, especificar el orden de los niveles nos permite comparar niveles:
```
levels(fac_conc)
fac_conc <- factor(fac_conc, levels = c("Bajo", "Medium", "Alto"), ordered = TRUE)
levels(fac_conc)
min(fac_conc)
```

>> **Ejercicio:** defina el directorio de trabajo, cargue el paquete `tidyverse`, lea los datos de los penguinos desde la carpeta `datasets` y confirme que los datos están bien.

```
library(tidyverse)
setwd(~/Dropbox/0_UNAL/Extension/2023/curso-R-2023-II)
penguins <- read.csv("../datasets/penguins_matrix2.txt", sep=",")
head(penguins)
```

## Base::plot: <a name = "plot"></a>
`plot` es una función genérica del paquete `base` de R para el trazado de objetos R. Es una buena función para hacer exploración general de los datos de forma fácil, pero es un poco difícil para hacer gráficas bonitas.

Para más detalles sobre los argumentos de los parámetros gráficos, por ejemplo, cambiar márgenes, mire la función `par`.

1. Gráfico general de comparaciones entre muestras:
```
names(penguins)
plot(penguins[,3:6], main = "Correlation plot")
```

2. Algo pareció interesante arriba? Podemos hacer la gráfica más específica para esta comparación:
```
plot(penguins$bill_length_mm, penguins$bill_depth_mm)
```

Para comprender mejor, quizás es importante poner por color y algunas otras cositas
```
plot(penguins$bill_length_mm, penguins$bill_depth_mm, pch = 19, col = factor(penguins$species))
abline(lm(penguins$bill_depth_mm ~ penguins$bill_length_mm), lwd = 5, col = "gray")

# Legend
legend("topright",
       legend = levels(factor(pen$species)),
       pch = 19,
       col = factor(levels(factor(pen$species))))
```
Tenga en cuenta que internamente la función almacenará los factores como números enteros (1 = "negro", 2 = "rojo", 3 = "verde", ...).

![points](https://www.datanovia.com/en/wp-content/uploads/dn-tutorials/ggplot2/figures/106-pch-in-r-r-pch-list-showing-different-point-shapes-in-rstudio-1.png)

3. Para salvar el imagen:
Se puede salvar en diferentes tipos de datos, como: `jpeg`, `pdf`, `png`...,
```
jpeg(file="Plot_lm.jpeg")
#CÓDIGO PARA PLOTAR
dev.off()
```
>> Además se puede especificar el tamaño. Como podemos descubrir cómo especificar el tamaño?

## `ggplot` <a name = "ggplot"></a>
El paquete `ggplot2` se basa en la **Gramática de los Gráficos (GG)**, que es un marco para la visualización de datos que disecciona cada componente de un gráfico en componentes individuales, creando **capas distintas**. Utilizando el sistema GG, podemos construir gráficos paso a paso para obtener resultados flexibles y personalizables.

![capas](https://r.qcbs.ca/workshop03/book-en/images/Layers_ggplot.png)

Las capas GG (layers) tienen nombres específicos:

![layers_names](https://r.qcbs.ca/workshop03/book-en/images/gglayers.png)

Para hacer un gráfico en `ggplot` de datos y mapeo de las variables son **requisitos**, mientras que las otras capas son para personalización adicional. Las capas que "no son necesarias" siguen siendo importantes, pero podrás generar un gráfico básico sin ellas.

![estructura_ggplot](https://r.qcbs.ca/workshop03/book-en/images/ggplot_basics.png)

### Gramática de las capas gráficas:
- **Datos:**
  - los datos, normalmente en formato tidyverse, proporcionarán los ingredientes para su gráfico
  - utilice las técnicas `dplyr` para preparar los datos para un formato de trazado óptimo
  - por lo general, esto significa que debe tener **una fila por cada observación** que desea trazar

- **Estética (aes)**, para hacer visibles los datos:
  - `x, y`: variable a lo largo de los ejes x e y
  - `color`: color de las variables según los datos
  - `fill`: color interior de la zona
  - `group`: a qué grupo pertenece una geom
  - `shape`: la figura utilizada para trazar un punto
  - `linetype`: tipo de línea utilizada (solid, dashed, etc.)
![line_type](http://www.sthda.com/sthda/RDoc/figure/ggplot2/ggplot2-line-type-line-type-1.png)  

  - `size`: escala de tamaño para una dimensión extra
  - `alpha`: transparencia del objeto geométrico

- **Objetos geométricos (geoms** - determina el tipo de gráfico)
  - `geom_point()`: gráfico de dispersión (scatterplot)
  - `geom_line()`: líneas que conectan puntos aumentando el valor de x
  - `geom_path()`: líneas que conectan puntos en secuencia de aparición
  - `geom_boxplot()`: gráfico de boxplots para variables categóricas
  - `geom_bar()`: gráficos de barras para el eje x categórico
  - `geom_histogram()`: histograma para eje x continuo
  - `geom_violin()`: Kernel distribución de la dispersión de datos
  - `geom_smooth()`: línea de función basada en datos

- **Facetas**
  - `facet_wrap()` o `facet_grid()` para múltiplos gráficos

- **Estadísticas**
  - similar a `geoms`, pero computada
  - muestran medias, recuentos y otros resúmenes estadísticos de los datos

- **Coordenadas** - ajuste de datos en una página
  - `coord_cartesian` para establecer límites
  - `coord_polar` para gráficos circulares
  - `coord_map` para diferentes proyecciones cartográficas

- **Temas**
  - parámetros visuales generales
  - fuentes, colores, formas, contornos

### Unir estas capas:
Los pasos básicos para crear un gráfico simple son:

1. Cree un objeto de gráfico simple: `plot.object <- ggplot()`
2. Añadir capas geométricas: `plot.object <- ggplot() + geom_*()`
3. Añadir capas de apariencia: `plot.object <- ggplot() + geom_*() + coord_*() + theme()`
4. Repita los pasos 2-3 hasta que esté satisfecho.
5. Imprimir: `plot.object` o `print(plot.object)`

Vamos poner las manos a trabajar! Pero antes no olvide de cargar el paquete `ggplot2`

1. **Histogram:**
```
ggplot(penguins, aes(x = body_mass_g, color = sex, fill = sex)) +
  geom_histogram(binwidth=30)
```

2. **Boxplot:**
```
ggplot(penguins, aes(x = species ,y = body_mass_g, fill = species)) +
  geom_boxplot(outlier.colour="red") +
  geom_jitter(shape=16, position=position_jitter(0.2))
```
Reordenar las variables:
```
penguins %>%
  mutate(name = factor(species, levels=c("Gentoo", "Adelie", "Chinstrap"))) %>%
    ggplot(aes(x = species ,y = body_mass_g, fill = species)) +
      geom_boxplot()
```

>> Ejercicio: haga un gráfico del tamaño del pico por año.

3. **Vamos hacer un gráfico similar que hicimos en el ejemplo de `base::plot`**
```
g_pen <- ggplot(data = penguins,           # Data
            aes(x = bill_length_mm,        # Your X-value
                y = bill_depth_mm,         # Your Y-value
               col = species)) +           # Aesthetics
      geom_point(shape=19, size = 5, alpha = 0.8) +  # Point
      geom_smooth(method = "lm") +         # Linear regression
      labs(title = "Relationship bill length and depth",  # Title
           x = "Bill length (mm)",         # X-axis title
           y = "Bill depth (mm)",          # Y-axis title
           col = "Species") +              # creates legend by species
      theme_classic() +                    # Apply a clean theme
      theme(title = element_text(size = 18, face = "bold"),
            text = element_text(size = 16))
g_pen
```

4. **Plots múltiples (facet):**
```
ggplot(data = penguins, aes(x = bill_length_mm, y = bill_depth_mm)) +
  geom_point() +
  facet_grid(vars(species), vars(year))  # Separa los plots en ventanas
```
>> Ejercicio: cambie el facet para `facet_wrap` y mire lo que pasa.

5. **Escoger cor manual:**
```
g_pen_cor <- g_pen +
              scale_colour_manual(
                  values = c("grey55", "orange", "skyblue"))
```
Tenga en cuenta que el orden de los colores sigue los factores `levels(factor(penguins$species))`

6. **Color gradiente:**
```
g_pen_grad <- ggplot(data = penguins, aes(x = bill_length_mm, y = bill_depth_mm, col = body_mass_g)) +
                geom_point(shape=19, size = 5, alpha = 0.8)

g_pen_grad + scale_colour_gradient(low = "blue", high = "red")
```

7. **Utilizar paletas de color:**
```
require(RColorBrewer)
display.brewer.all()
g_pen_cor <- g_pen +
              scale_colour_brewer(palette = "Dark2") +
              labs(title = "Color por paletas")
```

Me gusta este link para pensar cerca de colores: https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3

>> **Ejercicio:** basado en las actividades del curso realizadas durante la semana con los datos de penguinos, seleccione uno de las actividades que se fue realizada y realize una nueva gráfica.
