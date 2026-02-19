# Laboratorio 1 
**Universidad Militar Nueva Granada**  
**Asignatura:** Procesamiento Digital de Señales  
**Estudiantes:** [Lina Marcela Pabuena, ]  
**Fecha:** Febrero 202  
**Asignatura:** Procesamiento Digital de Señales  
**Título de la práctica:** Análisis estadístico de la señal 

## Objetivos 
- Identificar los estadísticos que describen una señal biomédica.  
- Obtener dichos estadísticos a partir de algoritmos programados en Python.  
- Comparar el cálculo de estadísticos hecho de forma manual (programando las fórmulas) con el uso de funciones predefinidas.  
- Importar, graficar y manipular señales fisiológicas en Python utilizando librerías como *matplotlib*.  
- Generar y capturar señales fisiológicas con (STM32 o DAQ).  
- Analizar el efecto del ruido en las señales mediante el cálculo de la relación señal-ruido (SNR).  
- Utilizar GitHub como herramienta de documentación y colaboración para reportar los resultados.  

# PARTE A
## Análisis Estadístico de la Señal

## Procedimiento y método
Las señales medidas de un entorno real, como las señales biomédicas, contienen tanto información relevante asociada al proceso fisiológico (amplitud y frecuencia), como información contaminante, denominada ruido, la cual puede deberse a interferencias eléctricas, movimiento del paciente o limitaciones del sistema de adquisición.
En esta práctica:
1. Se descargó una señal fisiológica desde [PhysioNet](https://physionet.org/), una base de datos pública de señales biomédicas.
2. Se importó la señal en Python y se graficó usando `matplotlib` para su visualización.
3. Se calcularon estadísticos descriptivos de dos maneras:
   - **Método manual:** programando las fórmulas desde cero.
   - **Funciones predefinidas:** usando librerías de Python.
4. Se analizaron los siguientes parámetros:
   - **Media de la señal**: indica el valor promedio.
   - **Desviación estándar**: mide cuánto varían los valores respecto a la media.
   - **Coeficiente de variación**: relación entre la desviación estándar y la media (porcentaje de variabilidad).
   - **Histograma de la señal**: muestra la distribución de valores de voltaje.
   - **Función de probabilidad**: describe la probabilidad de que la señal tome determinados valores.
   - **Asimetría (skewness)**: indica si la distribución de amplitudes está inclinada hacia valores positivos o negativos
   - **Curtosis**: mide el grado de concentración de los valores de la señal en torno a la media (si tiene colas más o menos pesadas que una distribución normal).

# Código en Python (Google Colab)
## Descarga y grafica de la señal con funciones
<pre> ```
python
# Importación de librerías necesarias
import numpy as np                  # Para manejo de arreglos y cálculos matemáticos
import matplotlib.pyplot as plt     # Para graficar la señal
!pip install wfdb                   # Instalación de la librería wfdb para leer señales biomédicas
import wfdb                          # Librería para trabajar con datos en formato PhysioNet
from scipy.stats import gaussian_kde, kurtosis # Para análisis estadístico adicional

# Montar Google Drive para acceder a los archivos
from google.colab import drive
drive.mount('/content/drive')

# Ruta del archivo descargado desde PhysioNet
record_name="/content/drive/MyDrive/GITHUB/100001_ECG"

# Lectura de la señal (señal1 contiene los datos, campos contiene información de cabecera)
señal1, campos = wfdb.rdsamp(record_name)
señal1

# Graficar la señal
plt.plot(señal1)                     # Dibuja la señal en función del tiempo
plt.xlabel("Tiempo(s)")              # Etiqueta del eje X
plt.ylabel("Voltaje(V)")             # Etiqueta del eje Y
plt.axis([3.5666e7,3.5668e7,-1500,2000]) # Límite de ejes para enfocar la región de interés
plt.grid()                           # Mostrar cuadrícula para mejor lectura
plt.show()
``` </pre>
## Gráfica de la señal ECG

<p align="center">
<img width="758" height="540" alt="señal11" src="https://github.com/user-attachments/assets/a300c2d4-4562-4c9f-bf4d-69e2726b4d73" />

## Interpretación de la gráfica

La gráfica anterior muestra un segmento de la señal fisiológica descargada desde la base de datos **PhysioNet**.  

- En el **eje X** se representa el tiempo (segundos).  
- En el **eje Y** se muestra el voltaje (microvoltios).  

Se observan **picos pronunciados** que corresponden a los **complejos QRS** del electrocardiograma, los cuales presentan una amplitud considerable en comparación con el resto de la señal.  

Entre los picos, se distinguen zonas de menor variación que representan las **ondas P y T**.  

Asimismo, se evidencia la presencia de **ruido** y variaciones irregulares en la línea base, lo cual es característico de señales biomédicas reales. Este ruido puede deberse a factores externos, **interferencias eléctricas** o **movimiento del paciente**. 
### Resultado: Señal fisiológica descargada

La gráfica corresponde a un segmento de una señal electrocardiográfica (ECG) obtenida desde la base de datos PhysioNet.  
El archivo fue descargado, subido a Google Drive y cargado en Python, donde fue almacenado en la variable **señal1**.  

Dado que la señal original contenía una gran cantidad de muestras, se realizó un ajuste de los ejes para **ampliar una ventana específica** y facilitar la visualización de las ondas características.  

En la figura se observan claramente los complejos QRS, con amplitudes que alcanzan valores cercanos a **±1500 µV**. Entre los picos principales se identifican segmentos isoeléctricos con variaciones menores, lo que corresponde al comportamiento normal de la señal entre cada latido. 

## Análisis estadístico de la señal
A partir de la señal fisiológica importada, se calcularon los principales estadísticos descriptivos.
La media permitió identificar el valor promedio de la señal, mientras que la desviación estándar mostró el grado de dispersión de los datos.
El coeficiente de variación se utilizó para normalizar dicha dispersión y facilitar la comparación con otras señales.
El histograma permitió visualizar la distribución de las amplitudes de la señal.
La asimetría (skewness) permitió identificar si la distribución de los valores se encuentra inclinada hacia uno de los extremos, lo cual es coherente con la presencia de picos de gran amplitud asociados a los complejos QRS.
Finalmente, la curtosis permitió analizar el grado de concentración de los valores alrededor de la media y la presencia de valores extremos.

### Código – Estadísticos con funciones
<pre> ```
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import skew, gaussian_kde

# Media
media = np.mean(señal1)
print(f"Media = {media}")

# Desviación estándar (muestral)
desviacion_muestra = np.std(señal1, ddof=1)
print(f"Desviación estándar = {desviacion_muestra}")

# Coeficiente de variación
coeficiente_variacion = np.std(señal1, ddof=0) / media * 100
print(f"Coeficiente de variación = {coeficiente_variacion}")

# Asimetría (skewness)
asimetria_funcion = skew(señal1)
print(f"Asimetría (función) = {asimetria_funcion}")

# Curtosis
n = len(señal1)
curtosis = np.sum(((señal1 - media) / desviacion_muestra)**4) / n
print(f"Curtosis = {curtosis}")

# Histograma
plt.figure(figsize=(8,4))
plt.hist(señal1, bins=100)
plt.title("Histograma de la señal ECG")
plt.xlabel("Voltaje (mV)")
plt.ylabel("Frecuencia")
plt.grid(True)
plt.show()

# Función de probabilidad (KDE)
data = np.ravel(señal1)
kde = gaussian_kde(data)
x_vals = np.linspace(min(data), max(data), 1000)

plt.figure(figsize=(8,4))
plt.plot(x_vals, kde(x_vals))
plt.title("Función de probabilidad (KDE)")
plt.xlabel("Valor de la señal")
plt.ylabel("Densidad de probabilidad")
plt.grid(True)
plt.show()
``` </pre>
### Resultados del análisis estadístico
- **Media:** 0.21505595733362035
- **Desviación estándar:** 461.2037350921682
- **Coeficiente de variación:** 214457.54777615666
- **Asimetría (función):** 1.86
- **Curtosis:** 122.85882529006771

## Resultados del análisis estadísticos
## Histograma
<p align="center"> <img width="686" height="560" alt="Captura de pantalla 2025-08-16 001720" src="https://github.com/user-attachments/assets/c63a407c-0d26-4663-a75c-06a09b496e3f" /> </p>

## Función de probabilidad
<p align="center"> <img width="731" height="555" alt="Captura de pantalla 2025-08-17 001331" src="https://github.com/user-attachments/assets/bc471a6b-8ea3-471d-bd55-a811e72b6415" /> </p>

## Análisis estadístico de la señal sin funciones
En esta sección se implementaron manualmente las fórmulas matemáticas de los estadísticos descriptivos, sin utilizar funciones predefinidas de librerías estadísticas.

### Código – Estadísticos sin funciones
<pre> ```
import math
import matplotlib.pyplot as plt

senal = list(señal1)
n = len(senal)

# Media
suma = 0
for x in senal:
    suma += x
media = suma / n

# Desviación estándar
suma_cuadrados = 0
for x in senal:
    suma_cuadrados += (x - media)**2
desv_std = math.sqrt(suma_cuadrados / n)

# Coeficiente de variación
coef_var = (desv_std / media) * 100

# Asimetría manual
suma_cubica = 0
for x in senal:
    suma_cubica += (x - media)**3
asimetria_manual = (suma_cubica / n) / (desv_std**3)

# Curtosis
suma_cuarta = 0
for x in senal:
    suma_cuarta += (x - media)**4
curtosis = (suma_cuarta / n) / (desv_std**4)

# Resultados
print("Media:", media)
print("Desviación estándar:", desv_std)
print("Coeficiente de variación:", coef_var)
print("Asimetría (manual):", asimetria_manual)
print("Curtosis:", curtosis)
``` </pre>

## Resultados del análisis estadísticos sin funciones
### Resultados numéricos
**Asimetría (manual)**: 1.87


