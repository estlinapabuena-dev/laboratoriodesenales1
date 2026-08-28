# Laboratorio 1 
**Universidad Militar Nueva Granada**  
**Asignatura:** Procesamiento Digital de Señales  
**Estudiantes:** [Lina Marcela Pabuena,Ralf Steven Castiblanco Solano ]  
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

## Resultados, gráfica de la señal 
<p align="center">
<img width="1068" height="486" alt="Captura de pantalla 2025-08-16 235352" src="https://github.com/user-attachments/assets/d1032b27-280d-48b5-b40d-6c9eb0e07bab" />
   
### Resultados estadísticos – Parte B (con funciones)

- **Media:** 1.219676066378888
- **Desviación estándar:** 0.4011725172544121
- **Coeficiente de variación:** 0.3289172660782452
- **Asimetría (función):** 1.86
- **Curtosis:** 4.689155028469519

## Histograma
<p align="center">
<img width="692" height="556" alt="Captura de pantalla 2025-08-16 235301" src="https://github.com/user-attachments/assets/16286fc9-2c26-4497-b59e-bed6386c8aea" />

## Función de probabilidad
<p align="center">
<img width="863" height="488" alt="Captura de pantalla 2025-08-16 235421" src="https://github.com/user-attachments/assets/4a3b16be-dd19-4d60-9b0b-ba242ce5cc8d" />

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

# PARTE C

## Relación Señal Ruido (SNR)

Esta relación es entre la amplitud de la señal y la amplitud del ruido. Generalmente está expresada en decibelios y se calcula mediante una sencilla ecuación. Implementar esta ecuación es muy importante para analizar una señal de ECG, en donde el ruido no deseado interfiere con la señal de interés. Cuando esta señal tiene valores muy altos quiere decir que la señal está más presente que el ruido, lo que indica buena calidad de la misma. Si ocurre lo contrario, el ruido domina la señal y los valores son bajos, lo que indica que la señal es de mala calidad [1].
<p align="center">
<img width="126" height="34" alt="Ecuación" src="https://github.com/user-attachments/assets/0cd0b9e0-d916-4042-8b5d-a4971bbbd382" />

## Análisis de la señal con adición de ruido y cálculo de la relación señal-ruido (SNR)

Para esta parte del laboratorio se trabajó sobre la señal fisiológica adquirida del generador de señales biológicas de la parte B. Se introdujeron tres tipos de ruido simulado para alterar la señal con el fin de analizar cómo estos afectan sus características estadísticas y la calidad de la extracción de datos. 

Se consideraron tres tipos de ruido o de contaminación:

## RUIDO GAUSSIANO:

Es un tipo de ruido aleatorio que sigue una distribución normal o gaussiana. Se caracteriza por una media y una desviación estándar [2]. 

## 1. Ruido Gaussiano
<pre> ```

ruido_gauss = np.random.normal(0, np.std(senal)*0.2, len(senal))  
senal_gauss = senal + ruido_gauss  
snr_gauss = calcular_snr(senal, ruido_gauss)  

plt.figure(figsize=(10,4))  
plt.plot(senal_gauss, color='blue')  
plt.title(f"Señal con ruido Gaussiano - SNR = {snr_gauss:.2f} dB")  
plt.xlabel("Muestras")  
plt.ylabel("Amplitud")  
plt.grid(True)  
plt.show()  
   ``` </pre>
## Gráfica ruido Gaussiano
<p align="center">
<img width="649" height="296" alt="image" src="https://github.com/user-attachments/assets/e0237c14-ae98-4fad-9acd-123d555c7e7d" />

## Análisis ruido Gaussiano

En esta adición de ruido no se desplazó la línea base y el nivel del ruido escala con la señal. El SNR se calculó con la señal limpia respecto al ruido añadido. Este ECG estaría simulando el ruido de los amplificadores, que son interferencias pequeñas y distribuidas. Se puede apreciar claramente en las ondas P y T, que son de baja amplitud y se ven afectadas. 

El valor resultante del ruido (24.22 dB) indica que la señal inicial del ECG aún es clara, ya que es un valor alto.

## RUIDO IMPULSO:

Se manifiesta en ráfagas breves y de alta amplitud. Se caracteriza por tener picos repentinos que causan una interferencia significativa [3].

## 2. Ruido de Impulso
<pre> ```

ruido_impulso = np.zeros(len(senal))  
num_impulsos = int(0.01 * len(senal))   # 1% de muestras con impulsos  
indices = np.random.choice(len(senal), num_impulsos, replace=False)  
ruido_impulso[indices] = np.max(senal) * np.random.choice([-1,1], num_impulsos)  

senal_impulso = senal + ruido_impulso  
snr_impulso = calcular_snr(senal, ruido_impulso)  

plt.figure(figsize=(10,4))  
plt.plot(senal_impulso, color='red')  
plt.title(f"Señal con ruido de Impulso - SNR = {snr_impulso:.2f} dB")  
plt.xlabel("Muestras")  
plt.ylabel("Amplitud")  
plt.grid(True)  
plt.show()  
   ``` </pre>

## Gráfica ruido impulso
<p align="center">
<img width="652" height="297" alt="image" src="https://github.com/user-attachments/assets/c695c258-ce32-4ab3-91f7-a48384abfe00" />

## Análisis ruido impulso

Debido a los picos se puede ver una anormalidad más fácilmente en una de las muestras. Este ruido podría simular malas conexiones del ECG, por ejemplo, electrodos mal conectados, movimiento brusco de los cables u otros factores. También se puede interpretar como complejos falsos.  

El valor resultante del ruido (14.32 dB) indica que el ruido está más presente en la señal. Este ECG está bastante afectado por los picos tan abruptos que generan complejos falsos o falsos eventos eléctricos del corazón. 

## RUIDO ARTEFACTO:

Este ruido es una perturbación no deseada causada por factores externos que pueden ser biológicos, electrónicos o generados por el mismo sistema de adquisición. En el ECG puede deberse a la actividad muscular, al parpadeo o a la interferencia electromagnética del entorno [4].

## 3. Ruido tipo Artefacto

<pre> ```

# (simulado con ondas senoidales lentas + un offset)

frecuencia_art = 0.01  
ruido_art = 0.5*np.max(senal) * np.sin(2*np.pi*frecuencia_art*np.arange(len(senal))/len(senal))  
senal_art = senal + ruido_art  
snr_art = calcular_snr(senal, ruido_art)  

plt.figure(figsize=(10,4))  
plt.plot(senal_art, color='green')  
plt.title(f"Señal con ruido tipo Artefacto - SNR = {snr_art:.2f} dB")  
plt.xlabel("Muestras")  
plt.ylabel("Amplitud")  
plt.grid(True)  
plt.show()  
   ``` </pre>

## Gráfica del ruido tipo artefacto
<p align="center">
<img width="1302" height="599" alt="image" src="https://github.com/user-attachments/assets/e8b3dbe1-b3c9-4932-a647-b32552ee131e" />

## Análisis del ruido tipo artefacto

Este ruido tipo artefacto en el código simula una "onda senoidal lenta" que está superpuesta en el ECG original, lo que genera una oscilación de baja frecuencia. Este patrón se podría interpretar como fenómenos respiratorios normales del tórax, impedancia de la piel o desplazamientos del electrodo. En esta gráfica se dificulta la clara identificación de las ondas P y T y se aprecia una modificación en el complejo QRS. Aunque no se distorsiona de forma drástica, sí se compromete el análisis de las tendencias y se genera error en la interpretación clínica, lo cual es importante para el diagnóstico de enfermedades cardíacas.  

Respecto al ruido resultante (29.22 dB), este es el mejor comparado con los dos anteriores, porque quiere decir que la señal está más presente que el ruido. La morfología del ECG no cambia de manera drástica aunque la línea base esté afectada.  

## Análisis de los resultados - Parte C

Esta simulación permitió observar cómo el ruido degrada la calidad de la señal según sus características estadísticas. También permitió identificar de qué tipo de ruido se trata para tener precaución en futuros análisis de señales electrocardiográficas. Resultados finales:
<pre>
   === Relación Señal-Ruido (SNR) ===
Ruido Gaussiano: 24.22 dB
Ruido de Impulso: 14.32 dB
Ruido tipo Artefacto: 29.22 dB
</pre>

# REFERENCIAS

[1] S. J. Patey and M. Wilson, “Processing, storage and display of physiological measurements,” Anaesth. Intensive Care Med., vol. 21, no. 5, pp. 261–266, May 2020, doi: 10.1016/j.mpaic.2020.03.001. [Accedido: 17-ago-2025]

[2] “El ruido gaussiano.”. Disponible en: (https://media4.obspm.fr/public/VAU/instrumentacion/observar/analizar/ruido-gaussiano/) [Accedido: 18-ago-2025].

[3] L. Sibley, “Common carrier transmission,” in Elsevier eBooks, 2002, pp. 18–38, doi: 10.1016/b978-075067291-7/50040-6.[Accedido: 18-ago-2025]

[4] Zeto Inc., “How Digital EEG Filters Impact EEG Signal Morphology,” *Zeto Inc. Blog*, [En línea]. Disponible en: https://zeto-inc.com/blog/eeg-signal-enhancement-digital-eeg-filters/. [Accedido: 18-ago-2025].
