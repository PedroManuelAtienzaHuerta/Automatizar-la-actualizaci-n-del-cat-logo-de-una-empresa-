# Automatizar-la-actualización-del-catálogo-de-una-empresa-

En una simulación de una situación real, en una frutería online, se necesitaba desarrollar un **sistema que actualizara la información del catálogo** con los datos proporcionados por sus proveedores.

Los proveedores envían los **datos como imágenes grandes con una descripción asociada** de los productos en dos archivos (.tiff para la imagen y .txt para la descripción). 

Es necesario convertir las imágenes en **jpeg** más pequeños y el texto en un **archivo HTML** que muestre la imagen y la descripción del producto. 

El contenido del archivo HTML debe cargarse en un **servicio web** que ya esté funcionando con Django. 

También es necesario recoger el **nombre y el peso** de todas las frutas de los archivos .txt y utilizar una **petición Python** para subirlo a su servidor Django.

Crearemos un script de Python que **procese** las imágenes y las descripciones y luego **actualice** el sitio web de la empresa para añadir los nuevos productos.

Una vez completada la tarea, se notifica al proveedor con un **correo electrónico** que indique el peso total de la fruta (en libras) que se había subido. 

El **correo electrónico tiene adjunto un PDF** con el nombre de la fruta y su peso total (en lbs).

Por último, paralelamente al funcionamiento de la automatización, queremos **comprobar la salud del sistema y enviar un correo electrónico si algo va mal.**

# Datos de los Proveedores 

Los proveedores nos envían un archivo con un directorio llamado **"supplier-data"**, que contiene subdirectorios llamados **"images"** y **"descriptions".**

Liste el contenido del directorio supplier-data utilizando el siguiente comando:

ls ~/supplier/data


Salida:

images descriptions

El subdirectorio **"images"** contiene imágenes de varias frutas, mientras que el subdirectorio **"descriptions"** tiene ficheros de texto que contienen la descripción de cada fruta. 

Puede echar un vistazo a cualquiera de estos archivos de texto utilizando el comando cat:

cat ~/supplier-data/descriptions 

Salida:

Mango
115 libs


# Trabajar con imágenes de proveedores:

En esta sección, escribiremos un script en Python llamado **changeImage.py** para procesar las imágenes de los proveedores. 

Usaremos la **biblioteca PIL** para actualizar todas las imágenes del directorio ~/supplier-data/images con las siguientes especificaciones:

- **Tamaño:** Cambiar la resolución de la imagen de 3000x2000 a 600x400 píxeles

- **Formato:** Cambiar el formato de imagen de .TIFF a .JPEG

El subdirectorio "images" contiene capas de transparencia alfa.

Por lo tanto, es mejor **convertir primero el formato RGBA de 4 canales a RGB de 3 canales** antes de procesar las imágenes. 

Utilice el método **convert("RGB")** para convertir la imagen RGBA a RGB.

Tras procesar las imágenes, se guardan en la misma ruta ~/supplier-data/images, pero con extensión.jpg

Cree y abra el archivo con el **editor nano.**

 nano ~/change_image.py
 
El archivo sería el siguiente:

#!/usr/bin/env python3
from PIL import Image
import os
path = "./supplier-data/images"
for f in os.listdir("./supplier-data/images"):
      if f.endswith(".tiff"):
           split_f = f.split(".")
           name = split_f[0] + ".jpeg"
           im = Image.open(path + f).convert("RGB")
           im.resize((600, 400)).save("./supplier-data/images/" + name, "JPEG")














