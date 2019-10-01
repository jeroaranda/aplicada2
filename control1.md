---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.1'
      jupytext_version: 1.1.7
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

<!-- #region {"colab_type": "text", "id": "uPmcSahcoMRS"} -->
# Control 1: Descripción y Análisis Exploratorio de Datos

Jerónimo Aranda 150344  
Emiliano Zambrano 157043  
Jorge Rotter  158391 


<!-- #endregion -->

```python colab={"base_uri": "https://localhost:8080/", "height": 198} colab_type="code" id="3U_7Mx2sxIpT" outputId="7be24031-ea48-423b-c18a-bd71a974c36a"
import io
import pandas as pd
import seaborn as sns
sns.set()
import matplotlib.pyplot as plt
plt.rcParams['figure.figsize'] = (20, 10)
```

## Lectura, limpieza y profiling

```python colab={} colab_type="code" id="6M0It85Rzhaf"
df = pd.read_csv("lista01.dat", sep="\s+", usecols=['ocupacion', 'educacion','ingreso','mujeres','prestigio','censo','tipo'])
df.head()
```

```python
df.groupby('censo').size().to_frame('n').sort_values('n', ascending=False).head()
```

```python
df[df.censo==8215]
```

`censo` no es la llave porque separan *slaughterers* en dos (sólo cambia el prestigio).

```python
df.groupby('ocupacion').size().to_frame('n').sort_values('n', ascending=False).head()
```

`ocupación` sí es la llave primaria.


## Clasificación de variables:

+ `ocupacion`: la llave única de la tabla.
+ `educacion`: continua (mayor a 0)
+ `ingreso`: continua (mayor a 0)
+ `mujeres`: continua 
+ `prestigio`: continua
+ `censo`: categórica sin orden (casi biyecta con la llave única)
+ `tipo`: categórica sin orden


## Exploratorio

```python colab={} colab_type="code" id="iypNJ7tHppKz"
df.plot.scatter(x='educacion',y='ingreso',c='prestigio', colormap='viridis')
```

Para empezar, el ingreso es creciente con respecto a la educación. Asimismo, mientras el nivel de educación crece, las ocupaciones se consideran más prestigiosas. Luego, el prestigio crece mientras el nivel de ingreso es mayor. También, los datos muestran que las ocupaciones con alto nivel de ingreso, educación y prestigio son escasas. 

```python
df.plot.scatter(x='mujeres',y='ingreso',c='prestigio',colormap='viridis')
```

El ingreso tiene una ligera tendencia decreciente a medida que el porcentaje de mujeres en la ocupación crece. Los datos no muestran una relación marcada entre el porcentaje de mujeres y el prestigio. Sin embargo, se observa que la cantidad de ocupaciones con alto nivel de prestigio es decreciente con respecto a la mujeres en la ocupación; es decir, a mayor porcentaje de mujeres se observa una caída en la cantidad de ocupaciones con prestigio mayor a 50. También, vale la pena destacar la acumulación de ocupaciones con un bajo porcentaje de mujeres, en particular aquellas con ingreso mayor a 5000 y prestigio mayor a 60.

```python
sns.distplot(df.ingreso)
```

```python
sns.distplot(np.log(df.ingreso))
```

```python
sns.boxplot(df.ingreso)
```

La mitad de las ocupaciones generan un ingreso de 6000 o menos, mientras que 5 ocupaciones generan 15000 o más. Las dos ocupaciones con mayor ingreso generan por lo menos 4.5 veces más que la mitad de todas las ocupaciones. Asimismo, el 75% de los trabajos generan a los más una tercera parte de lo que ingresan los dos mejor remunerados.

```python
sns.distplot(df.mujeres)
```

```python
sns.boxplot(df.mujeres)
```

El anterior gráfico es muy revelador en la categoría de mujeres. Al ser una variable que mide el porcentaje de mujeres por ocupación, se puede observar que el 50% de las ocupaciones incorporan menos de 2 mujeres por cada 10 empleados. Luego, alrededor del 75% de los empleados tienen 5  o menos mujeres cada 10 trabajadores.

```python
sns.distplot(df.educacion)
```

La educación es bimodal: gran parte de la muestra tiene una educación más o menos uniforme entre 6 y 11 y un grupo (poco más pequeño) de personas muy educadas.

```python
sns.distplot(df.prestigio)
```

El prestigio de las distintas profesiones tiene sesgo a la derecha y tienen una única moda cerca el 40.

```python
sns.scatterplot(x='ingreso', y='prestigio', hue='tipo', data=df)
```

En general, las profesiones *blue collar* están en el sector de bajo ingreso y bajo prestigio. Las de profesionista están en rangos más altos de ambas variables.

```python
tipo_to_numeric={'prof':2,'bc':0,'wc':1}
df['tipo_numeric']=df['tipo'].map(tipo_to_numeric)
```

```python
df['tipo_numeric'] = df.tipo_numeric+np.random.rand(df.shape[0])/10
```

```python
df.plot.scatter(x='tipo_numeric',y='ingreso',
                c='prestigio',colormap='viridis')
```

Los tres distintos tipos de ocupación son: 0 correspondiente al trabajo obrero, 1 correspondiente al oficinista, y 2 al profesional, gerencial o técnico. Es claro que el trabajo de tipo 2 tiene más prestigio que los otros dos. Asimismo, este tipo de trabajo es el único de los tres con ocupaciones que ganen más de 10000. De igual forma, las ocupaciones en esta categoría son mejor remuneradas que las otras dos en un cantidad considerable de casos. Por otra parte, el ingreso percibido por las ocupaciones de tipo obrera y oficinista es prácticamente el mismo. No obstante, el prestigio de los trabajos oficinistas es ligeramente superior.
