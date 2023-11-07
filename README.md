## LibreOffice Writter ⇔ MS Word 
Quizas uno de los temas mas recurrentes y de las principales trabas al migrar de Windows a Linux es el uso de aplicaciones de ofimática. 
Lo cierto es que mientras Microsoft no se decida a lanzar una versión nativa de su suite, esto no va a cambiar, peor aún, cuando suceda seguirá habiendo problemas tal cual los hay hoy en día entre versiones anteriores y actuales de los paquetes (que dios se apiade de las almas que tengan que usar Excel en MacOs).  

Este post intenta ser una guía para reducir un poco esos problemas, especialmente los de formato en el procesador de texto mas popular de Linux y MS Word. Se aclara que no es posible configurar una compatibilidad 1:1 pero esto reduce los problemas considerablemente. 

Facebook es horrible para estas guías, sigan las imagenes, mas tarde lo subo a GitHub 

### 1. APARIENCIA 
#### 1.1 Pestañas
Puede resultar incómodo cambiar a una barra de herramientas estándar después de acostumbrarse a los menús en pestañas de Office. Pero también puedes usar cintas en Office Writer. Haz clicen el menú *Ver* > *Interfaz de usuario* y selecciona *En Pestañas*. A continuación, haz clic en Aplicar a Writer o Aplicar a todo (recomendado).

#### 1.2 Íconos. 
Los íconos que ofrece LibreOffice son universales y en mi opinión, se entiende perfecto para qué es cada ícono sin mayor problema, además las versiones SVG de paquetes de íconos como Breeze estan bastante bien logrados, sin embargo habrá quien no se sienta cómodo con ellos, un usuario de DevianArt creo un paquete de íconos instalable como una extensión. 
El archivo lo encuentran acá: 
https://1drv.ms/u/s!ArgKmgFcmBYHhSQkPfyMZRnXX5LJ o acá https://github.com/vicrodh/LIbreWriterCalcAssets/blob/main/Office2013-theme-1.1.oxt

Con el archivo descargado, vayan al menú *Herramientas* > *Extensiones...* > [*Añadir*] seleccionan el archivo *Office2013-theme-1.1.oxt* recien descargado y luego cerrar, con eso quedó instalada la extensión y hará falta activar el paquete de íconos. 

Para ello, nuevamente abran el menú *Herramientas* > *Opciones* (Alt+F12) 

En el cuadro de dialogo que aparece, desde el panel izquierdo abran el arbol de LibreOffice, luego vayan a la opción Ver (1), después en Tema (2) y verán la opción de Office2013 (3) despues de presionar el botón Aplicar, pueden presionar Aceptar y tendrán iconografía similar a la de Office 2013.

#### 1.3 Reglas 
Las reglas arriba y a la izquierda son de las cosas que mas se extrañan la primera vez que abres un documento en LibreOffice, esta es fácil y para habilitar la regla vertica, basta con ir al Menú Ver > Reglas y asegurarse de marcar la opción de Regla vertical


### 2. FUENTES 
Los dos principales problemas en la compatibilidad de documentos de Word con los de LibreOffice son las fuentes y los margenes, cambiaremos ambas aquí, primero las fuentes. La mayoría de distribuciones decorosas tienen paquetes con las fuentes de Microsoft, aquí la instrucción para descargarlas e instalarlas: 

##### Distribuciones basadas en Debian:
`sudo apt update && sudo apt install ttf-mscorefonts-installer`
##### Distribuciones basadas en Arch (Hay un paque con las fuentes de win11, pero esta roto al instalarlo desde un ayudante, se puede instalar de forma manual, busquenlo en el AUR):
`yay -S ttf-ms-win10-auto`
##### Distribuciones basadas en RPM:
`sudo dnf install curl cabextract xorg-x11-font-utils fontconfig -y`
`sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm`

Si tienen una instalación de Windows en su equipo, pueden simplemente copiar la carpeta de fuentes de Windows y ahorrarse el paso anterior, en mi caso tengo mi instalación de Windows montada por default, asumamos que ustedes ya la han montado y cambiarán el siguiente comando para adecuarlo a su ruta: 
`sudo cp -rf /mnt/win-c/Windows/Fonts /usr/share/fonts/WinFonts`

Ahora, independientemente de caulquiera de los dos caminos que hayan tomado en el paso anterior, hay que actualizar el caché de las fuentes: 

`$ sudo fc-cache -rv `

Es probable que necesiten reiniciar Writer, haganlo y una vez que lo abran de nuevo, volvamos al menu *Herramientas* > *Opciones* 
Con el dialogo de Opciones abierto, abrimos el árbol *LibreOffice Writer* y vamos a la opción *Tipos de letra básicos*.

En todos los campos, salvo en el de Título, elegiremos Calibri como nuestra fuente predeterminada. En Título elegiremos *Calibri Light*. 
Luego hacemos clic en *Aplicar*/*Aceptar* 

Ahora ya tenemos un documento más similar a los de Office, ojo, hay un tema con la fuente Calibri y es que por default en Linux se ve horrible con un problema de anti-alias, dicho de otro modo pareciera que esta pixeleada. No pasa en todos las distribuciones Linux, pero si te sucede, aquí esta la solución: 

Puedes crear un archivo de configuración de fuentes, tienes dos opciones: 
1. Crearla en tu carpeta de configuración para tu usuario:
`~/.config/fontconfig/fonts.conf`
2. O bien para todos los usuarios del sistema (si hacerlo solo para tu usuario no te función prueba con este metodo):
`/usr/share/fontconfig/conf.default/10-anti-alias.conf`
El archivo deberá contener lo siguiente:
```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <match target="font">
  <test name="family" compare="contains">
    <string>Calibri</string>
  </test>
    <edit name="embeddedbitmap" mode="assign">
      <bool>false</bool>
    </edit>
  </match>
  <match target="font">
  <test name="family" compare="contains">
    <string>Cambria</string>
  </test>
    <edit name="embeddedbitmap" mode="assign">
      <bool>false</bool>
    </edit>
  </match>
</fontconfig>
```
Ahora nuevamente limpia el caché de fuentes:
`sudo fc-cache -rv`
Es probable que en este punto necesites reiniciar el servidor gráfico si los cambios no se aplican de inmediato, si no sabes como hacerlo Googlealo de acuerdo al entorno gráfico que uses o bien, reinicia 🤡. 
Ahora ya deberás tener fuentes bonitas, si te pasa el tema del antialias, analiza el contenido del archivo que recien creaste y añade las entradas de las fuentes que lo necesiten.

### 3. CAMBIAR LOS FORMATOS DE GUARDADO POR DEFECTO Y COMPATIBILIDAD
Podemos cambiar el formato predeterminado en el que LibreOffice guarda sus documentos. Ve otra vez al menu de *Herramientas* >  *Opciones*. Vaya al menú *'Cargar/Guardar'* y seleccione *'Microsoft Office'*.
Asegurate de marcar TODOS los check boxes del lado derecho. 

Una vez que lo hayas hecho, en el panel izquierdo, en la misma rama de *Cargar/guardar*, selecciona *General*, cambiaremos el formato para cargar y guardar default de ODT a DOCx de Office, seleccionalo en el selector *"Siempre guardar como:"*, este paso es meramente opcional pero si decides hacerlo, tambien es recomendable desmarcar la opción de *"Advertir cuando no se guarde en ODF ni en el formato predeterminado"*, solo para evitar el molesto mensaje avisando que no se esta guardando en el formato Open Document. 

Hacemos clic en Aplicar y sin cerrar la ventana, nos movemos nuevamente en el panel izquierdo, ahora vayamos a la rama: *LibreOffice Writer* y dentro de ella, a la opción de *Compatibilidad*. 
Ahí nos aseguraremos de tener marcadas las opciones: 
- *Reorganizar menú Formulario para compatibilidad con Microsoft*
- *Espacios posteriores compatibles con Word* 
Con estos dos campos marcados, hacemos clic en *"Usar como predeterminado"*, luego Aplicar y luego Aceptar. 

### 4. Plantilla predeterminada y estilos. 
Hasta este punto ya podríamos tener mayor compatibilidad con Word y si abrimos un documento creado en Word, este se verá considerablemente mejor que antes, pero nos falta atender el detalle de cuando nosotros creamos el documento inicial y lo compartimos con usuarios de Microsoft Office, para eso nos valdremos de la plantilla que Msft distribuye con su paqueteria. 
Si tiene un equipo con Windows, obtener la plantilla es sencillo, bastará con copiarla, esta se encuentra en la ruta: 

`C:\Users\<TU_USUARIO>\AppData\Roaming\Microsoft\Word`

(Si no tienen acá les dejo una: https://github.com/vicrodh/LIbreWriterCalcAssets/blob/main/Normal.dotm o en .dot si lo prefieren https://github.com/vicrodh/LIbreWriterCalcAssets/blob/main/Normal.dot)

El archivo se llama Normal.dotm basta con copiarlo a algun lugar de tu instalación en Linux y luego hacer doble clic en él, este deberá abrirse con LibreOffice (si tienen otros paquetes de ofimatica instalados, deberan hacer clic derecho y Abrir con... etc etc).

Esto nos abrira un documento en blanco, sin editar nada, ni modificar nada, vayamos al menu *Archivo* > *Plantillas* > *Guardar como plantilla* 

Pongan en nombre de plantilla: 
Normal
En categoría, elijan *"Mis plantillas"* 
Y por último, marquen la opción *"Establecer como plantilla predeterminada"*
Hacen clic en guardar, y listo. 

Y nada, ahora tienen un LibreOffice Writter considerablemente más compatible con Microsoft Office. Reitero el disclaimer original, no hay modo de alcanzar 100% de compatibilidad, pero si siguen los pasos aquí mencionados al menos los documentso no se harán m*rda cuando tengan que trabajar en un paquete u otro. 
Intentaré luego hacer una guía simiar para Excel/Calc, sin embargo la mayor diferencia en Excel vs Calc es el tema de los macros y no hay mucho que hacer al respecto ahí. 

Espero que a mas de uno le sirva. :) 
