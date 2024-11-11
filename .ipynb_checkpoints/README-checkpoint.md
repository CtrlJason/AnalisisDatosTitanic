# Proyecto de Análisis de Datos de Pasajeros del Titanic

## Requisitos para correr el proyecto

Asegúrate de tener instalado lo siguiente en tu computadora:
1. Python (versión 3.6 o superior)
2. pip (normalmente viene incluso en Python 3)
3. Extensión Jupyter Notebook para Visual Studio Code o un IDE con esto incluido
##### Estructura de carpetas del proyecto
```
Titanic/
├── data/
│   └── train.csv     # Dataset de pasajeros del Titanic
├── notebooks/
│   └── main.ipynb    # Notebook principal (Lo utilizamos para realizar el analisis del proyecto)
├── scripts/          
│   └── main.py       # Script principal Python (No se utiliza en el proyecto, sin embargo puede ser de utilidad en un futuro)
└── README.md         # Documentación del proyecto
```

### Pasos para ejecutar el proyecto

1. Crear un entorno virtual para manejar las dependencias del proyecto, esto es opcional, sin embargo, es buena práctica realizarlo.

```
# Ejecuta los comandos en tu terminal dentro del proyecto

# Crear un entorno virtual llamado 'venv'
python -m venv venv

# Activar el entorno virtual

# En Windows
venv\Scripts\activate

# En macOS/Linux
source venv/bin/activate
```

2. Instala las Dependencias
```
# Ejecuta el comando en tu terminal dentro del proyecto
pip install pandas
pip install jupyter
```
3. Ejecuta jupyter para poder iniciar el protecto
```
# Ejecuta el siguiente comando para inicializar jupyter
jupyter lab
```

## Análisis del Dataset
1. En este proyecto utilizamos un dataset de pasajeros del Titanic, que tiene 891 registros y 12 columnas, con información sobre cada pasajero, como su nombre, la clase, el género, la edad, el número de hermanos o cónyuges y el precio del pasaje.
Para mostrar todas las columnas utilizamos el siguiente codigo: df.describe()
``` df.describe()  # Estadísticas descriptivas para columnas numéricas ```
3. Los tipos de variables que existen son:
   - string (cadena de caracteres) Las cadenas de texto las utilizamos mayormente para realizar conversión del texto, ejemplo: pasar de mayúsculas a minúsculas JUAN -> juan y mucho más
   - int (números enteros) Los numeros enteros los usamos comúnmente para realizar operaciones matemáticas como sumas, restas, multiplicaciones o divisiones, adicionalmente, pero se usan más que todo si queremos saber el resultado redondeado de una operación, como la cantidad de pasajeros.
   - floats (números flotantes) Utilizamos los flotantes para realizar operaciones matemáticas o sacar resultados donde necesitamos saber los decimales como por ejemplo saber el precio de una zona del barco.
4. Los tipos de datos, los más relevantes serían los strings, ya que con ellos hacemos la separación de los datos, en este caso de los nombres de los pasajeros; sin embargo, para el análisis de supervivencia el tipo de dato más relevante serían los int.
Para mostrar los tipos de datos utilizamos comunmente el siguiente codigo: df.info()
``` df.info()  # Muestra los tipos de datos y valores nulos  ```
6. En algunas columnas hay datos nulos, estas se deben ser tratados, en este caso podrían ser convertidos como un promedio general de todos los datos de una columna, por ejemplo la edad de los pasajeros para realizar un aproximado.
Para ver cuanto nulos tenemos por columnas utilizamos el siguiente codigo: df.isnull().sum()
``` df.isnull().sum()  # Recuento de valores nulos por columna ```
8. En caso de que haya columnas duplicadas, lo correcto sería eliminarlos si no son requeridas, también se puede realizar con valores, pero hay que tener cuidado, ya que puede que no puedan servir de alguna manera en un fututo, lo recomendable es transformarlos al final.


Las columnas principales incluyen:

- `Survived`: Muestra si el pasajero sobrevivió (1) o no (0).
- `Pclass`: La clase del pasajero (1 = primera clase, 2 = segunda clase, 3 = tercera clase).
- `Sex`: Género del pasajero.
- `Age`: Edad del pasajero. Esta columna contiene registros nulos.
- `Cabin`: El número de la cabina. También tiene registros nulos.
- `Embarked`: Puerto de embarque, con valores como *C* (Cherburgo), *Q* (Queenstown), y *S* (Southampton). Hay algunos valores nulos en esta columna.

## Explicación de la Estrategia ETL

### Extracción

1. Ubicación del Archivo: El archivo train.csv ubicado en la carpeta data/, dentro del proyecto.
2. Carga del Dataset con Pandas: En el archivo de notebook (main.ipynb), utilizamos la biblioteca pandas instalada con pip de Python para cargar el dataset en un DataFrame de Pandas. El código de carga es el siguiente:

```
import pandas as pd

# Cargar el dataset desde la carpeta de datos
df = pd.read_csv('data/train.csv')
```

### Transformación

1. Se tomó la columna Name y con base a ella se crearon dos nuevas columnas, FirstName y LastName

***Convertimos todos los nombre a minúsculas para trabajar más fácil con ellos***

```
# Transformamos los datos a string y luego los convertimos a minusculas con el metodo lower()

df['Name'] = df['Name'].str.lower()
```

***Separamos los nombres***

```
# Separamos los nombre por comas con el metodo split()

df['Name'].str.split(',')
```

***Creamos las columnas FirsName y LastName***
Se crean para poder visualizar la información de los pasajeros de una manera más estructurada, adicionalmente si queremos hacer consultas solo de los primeros nombres es mucho más sencillo trabajar con los datos, por si queremos saber cuantas personas hay en el barco el nombre *Juan* por ejemplo.

```
# Con el metodo get() obtenemos los datos segun su indice

# Primeros nombres
df['FirstName'] = df['Name'].str.split(',').str.get(0)

# Para obtener los apellidos primero separamos los primeros nombres con los apellidos con split(,) y los obtenemos con get(1) haciendo referencia a los apellidos luego separamos los titulos con split(.) nuevamente y obtenemos los apellifos con get(1) para no tomar los titulos

# Apellidos
df['LastName'] = df['Name'].str.split(',').str.get(1).str.split('.').str.get(1)

# Usamos head() para consultar las primeras filas del DataFrame

# Consultamos los datos
df[['Name', 'FirstName', 'LastName']].head()
```

| PassengerId | Name                                 | FirstName   | LastName  |
|-------------|--------------------------------------|-------------|-----------|
| 1           | Braund, Mr. Owen Harris              | Braund      | Owen Harris |
| 2           | Cumings, Mrs. John Bradley           | Cumings     | John Bradley |
| 3           | Heikkinen, Miss. Laina               | Heikkinen   | Laina     |
| 4           | Futrelle, Mrs. Jacques Heath         | Futrelle    | Jacques Heath |
| 5           | Allen, Mr. William Henry             | Allen       | William Henry |

2. Se extrajeron los titulos de las personas tales como miss, Mrs, etc. Para poder visualizarlo mucho mejor e identificar a cada persona por su título

*Separamos los títulos de "Name" (nombre completo con título)*

```
df['Name'].str.split(',').str.get(1).str.split('.').str.get(0)
```

***Creamos la columna "Status" para almacenar los títulos***
Creamos esta columna para saber las profesiones o rangos de cada pasajero, esto con el fin de determinar con exactitud cuantas personas con un rango determinando existen, compararlo posteriormente con otros rangos o saber qué rangos pertenecían más a ciertas clases del barco.

```
df['Status'] = df['Name'].str.split(',').str.get(1).str.split('.').str.get(0)

# Consultamos los datos
df[['Status', 'FirstName', 'LastName']].head()
```

| PassengerId | Status          | FirstName   | LastName  |
|-------------|-----------------|-------------|-----------|
| 1           | Mr.             | Braund      | Owen Harris |
| 2           | Mrs.            | Cumings     | John Bradley |
| 3           | Miss.           | Heikkinen   | Laina     |
| 4           | Mrs.            | Futrelle    | Jacques Heath |
| 5           | Mr.             | Allen       | William Henry |

### Carga

1. Guardamos el DataFrame en un archivo cvs en la carpeta data de nuestro proyecto

```
# Guardar el DataFrame transformado como un archivo CSV en la carpeta data de nuestro proyecto
df.to_csv('data/transformed_data.csv')
```

2. Guardamos la información en la una base de datos
Se realizará en un futuro.

### Visualización

Para la visualización se utilizaron las bibliotecas ***Matplotlib*** y ***Seaborn***, podemos utilizar la libreria matplotlib; sin embargo, con seaborn, los gráficos se visualizan de una forma mucho más atractiva y además son más fáciles de usar.

### Análisis visual de los Datos
Primero importamos las librerías ***Matplotlib*** y ***Seaborn***
```
import matplotlib.pyplot as plt
import seaborn as sns
```

#### Ejemplo utilizando Matplotlib
```
x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]

plt.plot(x, y)
plt.title("Gráfico de Línea")
plt.xlabel("Eje X")
plt.ylabel("Eje Y")
plt.show()
```
![](https://i.ibb.co/ygsZcFn/imagen-2024-11-11-173119747.png)

#### Ejemplo utilizando Seaborn
```
data = sns.load_dataset("tips")

# Gráfico de dispersión con regresión
sns.regplot(x="total_bill", y="tip", data=data)
plt.title("Relación entre la cuenta total y la propina")
plt.show()
```
![](https://i.ibb.co/N6dgW9N/imagen-2024-11-11-172721389.png)

#### Graficas

Se realizan 5 gráficas para poder visualizar lo siguiente: Distribución de la Edad, Supervivencia por Género, Supervivencia por Clase de Pasajero, Relación entre Edad y Tarifa (Fare) y Mapa de Calor de Correlaciones, para cada una se utilizarán varias gráficas, serán 5 para representar correctamente cada uno de estos puntos
Las gráficas que utilizaremos para cada una serán:
- *Hisograma* Para Distribución de la Edad
- *Countplot* Para Supervivencia por Género
- *Countplot* Nuevamente para Supervivencia por Clase de Pasajero
- *scatterplot* Relación entre Edad y Tarifa (Fare)
- *Matriz de Correlación* Para Mapa de Calor de Correlaciones

1. Distribución de la Edad
```
plt.figure(figsize=(10, 6))  # Establece el tamaño de la figura
sns.histplot(df['Age'].dropna(), bins=30, kde=True)  # Crea el histograma con KDE
plt.title("Distribución de Edad de los Pasajeros")  # Título del gráfico
plt.xlabel("Edad")  # Etiqueta del eje X
plt.ylabel("Frecuencia")  # Etiqueta del eje Y
plt.savefig("viz/age_distribution.png")  # Guarda el gráfico en un archivo
plt.show()  # Muestra el gráfico
```
![](https://i.postimg.cc/J76hSjw0/image.png)

2. Supervivencia por Género
```
plt.figure(figsize=(8, 6))  # Establece el tamaño de la figura
sns.countplot(x='Sex', hue='Survived', data=df)  # Crea el countplot
plt.title("Supervivencia por Género")  # Título del gráfico
plt.xlabel("Género")  # Etiqueta del eje X
plt.ylabel("Número de Pasajeros")  # Etiqueta del eje Y
plt.legend(["No Sobrevivió", "Sobrevivió"])  # Leyenda para los valores de Survived
plt.savefig("viz/survival_by_gender.png")  # Guarda el gráfico en un archivo
plt.show()  # Muestra el gráfico
```
![](https://i.postimg.cc/Rq5Lvy3Q/image.png)

3. Supervivencia por Clase de Pasajero
```
plt.figure(figsize=(8, 6))  # Establece el tamaño de la figura
sns.countplot(x='Pclass', hue='Survived', data=df)  # Crea el countplot por clase y supervivencia
plt.title("Supervivencia por Clase de Pasajero")  # Título del gráfico
plt.xlabel("Clase")  # Etiqueta del eje X
plt.ylabel("Número de Pasajeros")  # Etiqueta del eje Y
plt.legend(["No Sobrevivió", "Sobrevivió"])  # Leyenda para los valores de Survived
plt.savefig("viz/survival_by_class.png")  # Guarda el gráfico en un archivo
plt.show()  # Muestra el gráfico
```
![](https://i.postimg.cc/fb84kjf0/imagen-2024-11-11-180551382.png)

4. Relación entre Edad y Tarifa (Fare)
```
dfc = df.copy()
df['Survived'] = df['Survived'].astype('category')
plt.figure(figsize=(10, 6))  # Establece el tamaño de la figura
sns.scatterplot(x='Age', y='Fare', hue='Survived', data=df)  # Crea el scatterplot
plt.title("Relación entre Edad y Tarifa")  # Título del gráfico
plt.xlabel("Edad")  # Etiqueta del eje X
plt.ylabel("Tarifa")  # Etiqueta del eje Y
plt.legend(["No Sobrevivió", "Sobrevivió"])  # Leyenda para los valores de Survived
plt.savefig("viz/age_vs_fare.png")  # Guarda el gráfico en un archivo
plt.show()  # Muestra el gráfico
```
![](https://i.postimg.cc/8kHyGPwj/imagen-2024-11-11-180641443.png)

5. Mapa de Calor de Correlaciones
```
plt.figure(figsize=(10, 8))
correlation_matrix = df[['Pclass', 'Survived', 'Age', 'SibSp', 'Parch', 'Fare']].corr()
sns.heatmap(correlation_matrix, annot=True, cmap="coolwarm")
plt.title("Mapa de Calor de Correlaciones")
plt.savefig("viz/heatmap_correlation.png")
plt.show()
```
![](https://i.postimg.cc/vBm46hFR/imagen-2024-11-11-180725137.png)

