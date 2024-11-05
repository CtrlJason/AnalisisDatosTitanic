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
│   └── main.ipynb    # Notebook principal
├── scripts/          
│   └── main.py       # Script principal Python
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
2. Los tipos de variables que existen son: string (cadena de caracteres), int (números enteros) y floats (números flotantes).
3. En este caso de los tipos de datos los más relevantes serían los strings, ya que con ellos hacemos la separación de los datos, en este caso de los nombres de los pasajeros; sin embargo, para el análisis de supervivencia el tipo de dato más relevante serían los int.
4. En algunas columnas hay datos nulos, estas se deben tratar como un promedio general de todos los datos de la columna para realizar un aproximado.

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

1. Guardamos el DataFrame en un archivo cvs

```
# Guardar el DataFrame transformado como un archivo CSV
df.to_csv('data/transformed_data.csv')
```

2. Guardamos la información en la una base de datos
Se realizará en un futuro.
