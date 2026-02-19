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

## Análisis de resultados – Parte A

En la Parte A se trabajó con una señal fisiológica de ECG obtenida desde la base de datos PhysioNet.
Tras su importación en Python y su almacenamiento en la variable señal1, se realizó un recorte de la señal con el fin de mejorar la visualización de sus componentes característicos, dado que la longitud original era demasiado extensa para un análisis gráfico claro.

Posteriormente, se calcularon los estadísticos descriptivos mediante dos enfoques:

Implementación manual de las fórmulas matemáticas.

Uso de funciones predefinidas de librerías como NumPy y SciPy.

Los resultados obtenidos fueron consistentes entre ambos métodos, lo cual valida la correcta implementación de las expresiones matemáticas.

Los valores de **la media y la desviación estándar** mostraron que la señal oscila alrededor de un promedio cercano a cero, con una dispersión significativa asociada a la presencia de los picos característicos de los complejos QRS.
**El coeficiente de variación** evidenció un alto grado de variabilidad relativa, lo cual es coherente con la naturaleza no estacionaria de las señales biomédicas.

**El histograma** permitió visualizar la distribución de amplitudes, donde la mayor parte de los datos se concentra alrededor de valores cercanos a cero, mientras que los picos de mayor amplitud aparecen con menor frecuencia.
**La función de probabilidad** confirmó esta tendencia, mostrando que las amplitudes extremas tienen baja probabilidad de ocurrencia.

Finalmente, **la curtosis** indicó que la distribución es más apuntada que una distribución normal estándar, reflejando la presencia de valores extremos propios de la señal ECG.
Adicionalmente, **el cálculo de la asimetría** evidenció que la distribución presenta una cola más pronunciada hacia valores positivos, lo cual concuerda con la morfología típica de este tipo de señales fisiológicas.

## PARTE B

En la Parte B del laboratorio se realizó la adquisición experimental de una señal fisiológica utilizando un generador de señales biológicas conectado a un DAQ (Data Acquisition System).
Para la conexión se inició con la lectura manual del equipo, identificando las entradas necesarias para el montaje. Posteriormente, se implementaron dos jumpers: uno para la terminal de tierra (GND) y otro para la entrada analógica (AI – Analog Input), encargada de recibir la señal proveniente del generador.

Una vez asegurada la conexión, se procedió a la instalación del driver correspondiente y a la configuración del software de adquisición. Se estableció la captura de 100 muestras, con el fin de evitar el registro excesivo de datos y obtener una señal clara para el análisis.
Finalmente, los datos adquiridos se exportaron en formato .csv y se almacenaron para su posterior procesamiento en Google Colab mediante Python.

### Almacenamiento de la señal en variables
<pre> ```
tiempo = df.iloc[:, 0].values
senal2 = df.iloc[:, 1].values
``` </pre>

### Análisis estadístico de la señal – Con funciones
<pre> ```
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import kurtosis, skew
import seaborn as sns

# Gráfica de la señal
plt.figure(figsize=(10,4))
plt.plot(senal2)
plt.title("Señal fisiológica medida en laboratorio")
plt.xlabel("Muestras")
plt.ylabel("Amplitud")
plt.grid(True)
plt.show()

# Estadísticos
media = np.mean(senal2)
desv = np.std(senal2)
cv = desv / media
asimetria_func = skew(senal2)
curt = kurtosis(senal2)

print("Media:", media)
print("Desviación estándar:", desv)
print("Coeficiente de variación:", cv)
print("Asimetría (función):", asimetria_func)
print("Curtosis:", curt)

# Histograma
plt.hist(senal2, bins=50, density=True)
plt.title("Histograma de la señal medida")
plt.xlabel("Amplitud")
plt.ylabel("Frecuencia")
plt.show()

# Función de probabilidad
plt.figure(figsize=(8,4))
sns.kdeplot(senal2, fill=True)
plt.title("Función de probabilidad de la señal")
plt.xlabel("Amplitud")
plt.ylabel("Densidad de probabilidad")
plt.grid(True)
plt.show()
``` </pre>

### Resultados estadísticos – Parte B (con funciones)

- **Media:** 1.219676066378888
- **Desviación estándar:** 0.4011725172544121
- **Coeficiente de variación:** 0.3289172660782452
- **Asimetría (función):** 1.86
- **Curtosis:** 4.689155028469519

  ### Análisis estadístico de la señal – Sin funciones
<pre> ```
import numpy as np
import matplotlib.pyplot as plt

n = len(senal2)

# Media
suma = 0
for x in senal2:
    suma += x
media_manual = suma / n

# Desviación estándar
suma_cuadrados = 0
for x in senal2:
    suma_cuadrados += (x - media_manual)**2
desv_manual = (suma_cuadrados / (n-1))**0.5

# Coeficiente de variación
cv_manual = desv_manual / media_manual if media_manual != 0 else float("inf")

# Asimetría
suma_cubica = 0
for x in senal2:
    suma_cubica += (x - media_manual)**3
asimetria_manual = (suma_cubica / n) / (desv_manual**3)

# Curtosis
suma_cuarta = 0
for x in senal2:
    suma_cuarta += (x - media_manual)**4
curtosis_manual = (suma_cuarta / n) / (desv_manual**4)

print("Media (manual):", media_manual)
print("Desviación estándar (manual):", desv_manual)
print("Coeficiente de variación (manual):", cv_manual)
print("Asimetría (manual):", asimetria_manual)
print("Curtosis (manual):", curtosis_manual)

# Histograma
plt.hist(senal2, bins=50)
plt.title("Histograma de la señal (manual)")
plt.xlabel("Amplitud")
plt.ylabel("Frecuencia")
plt.grid(True)
plt.show()
``` </pre>

### Resultados estadísticos – Parte B (sin funciones)
- **Media (manual):** 1.2196760663788881
- **Desviación estándar (manual):** 0.40319355028341297
- **Coeficiente de variación (manual):** 0.33057429050031245
- **Asimetría (manual):** 1.87
- **Curtosis (manual):** 7.5361408434029675

## Análisis de resultados – Parte B

Al observar la señal registrada experimentalmente, se evidenció un comportamiento más irregular que el observado en la señal descargada desde PhysioNet en la Parte A, lo cual es coherente con el hecho de tratarse de una adquisición real sujeta a ruido e interferencias externas.

Los valores de la media se mantuvieron cercanos a un punto de referencia estable, mientras que la desviación estándar resultó mayor, reflejando una mayor dispersión de los datos.
El coeficiente de variación confirmó el incremento en la variabilidad relativa de la señal.

La asimetría positiva obtenida tanto por el método manual como por funciones indica que la distribución de la señal presenta una cola hacia valores positivos, asociada a la presencia de picos de mayor amplitud y al ruido inherente al proceso de adquisición.
Por último, la curtosis evidenció colas más pronunciadas, lo que indica la presencia de valores atípicos en la señal experimental.

## PARTE C – Relación Señal Ruido (SNR)

La Relación Señal-Ruido (SNR) expresa la relación entre la potencia de la señal útil y la potencia del ruido, y se calcula generalmente en decibelios (dB).
Este parámetro es fundamental para evaluar la calidad de una señal ECG, ya que permite determinar qué tanto el ruido interfiere con la señal de interés.

### Análisis de la señal con adición de ruido
<pre> ```
import numpy as np
import matplotlib.pyplot as plt

def calcular_snr(senal, ruido):
    return 10 * np.log10(np.sum(senal**2) / np.sum(ruido**2))

senal = senal2
``` </pre>

### 1. Ruido Gaussiano
<pre> ```
ruido_gauss = np.random.normal(0, np.std(senal)*0.2, len(senal))
senal_gauss = senal + ruido_gauss
snr_gauss = calcular_snr(senal, ruido_gauss)

plt.figure(figsize=(10,4))
plt.plot(senal_gauss)
plt.title(f"Señal con ruido Gaussiano - SNR = {snr_gauss:.2f} dB")
plt.xlabel("Muestras")
plt.ylabel("Amplitud")
plt.grid(True)
plt.show()
``` </pre>

### 2. Ruido de Impulso
<pre> ```
ruido_impulso = np.zeros(len(senal))
num_impulsos = int(0.01 * len(senal))
indices = np.random.choice(len(senal), num_impulsos, replace=False)
ruido_impulso[indices] = np.max(senal) * np.random.choice([-1, 1], num_impulsos)

senal_impulso = senal + ruido_impulso
snr_impulso = calcular_snr(senal, ruido_impulso)

plt.figure(figsize=(10,4))
plt.plot(senal_impulso)
plt.title(f"Señal con ruido de Impulso - SNR = {snr_impulso:.2f} dB")
plt.xlabel("Muestras")
plt.ylabel("Amplitud")
plt.grid(True)
plt.show()
``` </pre>

### 3. Ruido tipo Artefacto
<pre> ```
frecuencia_art = 0.01
ruido_art = 0.5 * np.max(senal) * np.sin(2*np.pi*frecuencia_art*np.arange(len(senal))/len(senal))
senal_art = senal + ruido_art
snr_art = calcular_snr(senal, ruido_art)

plt.figure(figsize=(10,4))
plt.plot(senal_art)
plt.title(f"Señal con ruido tipo Artefacto - SNR = {snr_art:.2f} dB")
plt.xlabel("Muestras")
plt.ylabel("Amplitud")
plt.grid(True)
plt.show()
``` </pre>

### Análisis de resultados – Parte C
<pre> ```
=== Relación Señal-Ruido (SNR) ===
Ruido Gaussiano: 24.22 dB
Ruido de Impulso: 14.32 dB
Ruido tipo Artefacto: 29.22 dB
``` </pre>

El ruido de impulso fue el que más degradó la señal, mientras que el ruido tipo artefacto presentó el mayor SNR, conservando mejor la morfología general del ECG.

# DIAGRAMAS DE FLUJO

## REFERENCIAS

[1] S. J. Patey and M. Wilson, Processing, storage and display of physiological measurements, Anaesth. Intensive Care Med., vol. 21, no. 5, pp. 261–266, 2020.

[2] “El ruido gaussiano.” Disponible en: https://media4.obspm.fr/public/VAU/instrumentacion/observar/analizar/ruido-gaussiano/

[3] L. Sibley, Common carrier transmission, Elsevier, 2002.

[4] Zeto Inc., “How Digital EEG Filters Impact EEG Signal Morphology.” Disponible en: https://zeto-inc.com/blog/eeg-signal-enhancement-digital-eeg-filters/
